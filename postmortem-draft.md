# Descrição do problema

Pico de latencia nos percentis p99 a cada 5 min, esse pico ocorre entre o processamento do SDK da Azure e a propria Azure. Junto com este pico ocorre aumento de CPU.

Contexto:

- Aplicação sob carga de 500tps 
- Integração com cosmosdb
- 70k RU do cosmosdb
- Default session selecionada: Eventual
- Cluster AKS com VM Standard_DS2_v2 maquina de uso geral com 2VCPU e 4GB de RAM 
- Golang versão 1.26.1
- HttpTransport do Client HTTP parametrizado
- Se habilitar a preferencia de região a latencia aumenta


# Diagnóstico

### 1 Analise: Teoria da Causa do Bloqueio de 5 em 5 Minutos

A cada 5 minutos, o SDK do Cosmos DB para Go realiza uma chamada obrigatória de background para atualizar o mapa de roteamento de partições do banco de dados.
Em cenários de alta carga (500 TPS), essa tarefa de background exige processamento síncrono e imediato de rede e memória. Como a aplicação roda como um Sidecar dividindo uma máquina de apenas 2 vCPUs (Standard_DS2_v2) sem limites de CPU definidos no Kubernetes, ocorre uma Disputa de Hardware:

   1. O Go 1.26 identifica as 2 vCPUs do host e define automaticamente GOMAXPROCS=2 (apenas duas threads lógicas de execução).
   2. No 5º minuto, o app principal e o Sidecar tentam usar a CPU agressivamente ao mesmo tempo.
   3. Faltam núcleos físicos para processar tudo em paralelo. O agendador do Go deixa a tarefa de atualização do Cosmos DB esperando na fila (CPU/Thread Starvation).
   4. Como o mapa de rotas antigo fica "congelado" aguardando o novo, todo o pipeline de conexões HTTP do SDK trava, retendo as conexões e fazendo a latência estourar.

### 2 Analise: Gargalo de Contenção de Mutex no SDK Go (azcosmos)

Identificamos que as degradações periódicas de latência coincidem com o intervalo fixo de recarga do `GlobalEndpointManager` do SDK oficial da Microsoft (azcosmos), regido pela constante interna `defaultRefreshTimeInterval = 5 * time.Minute`.

Diferente de SDKs para runtimes com suporte nativo a threads pesadas (como .NET ou Java, que isolam esta tarefa em um Background Timer), o SDK de Go adota uma estratégia de Lazy Refresh síncrono no Hot Path das requisições. A cada 300 segundos, a primeira requisição operacional de banco de dados assume a responsabilidade de realizar um GET administrativo na conta do Cosmos DB para atualizar a topologia de regiões.

Sob carga de 500 TPS, este comportamento gera uma contenção severa de concorrência: o método `globalEndpointManager.Update()` adquire um exclusão mutua `(sync.Mutex.Lock())`, enfileirando síncronamente todas as requisições subsequentes recebidas na janela de milissegundos em que o I/O de rede administrativo está sendo resolvido. O comportamento simula um congelamento temporário do cliente HTTP, seguido de um efeito Thundering Herd sobre o pool de conexões TCP no momento da liberação do Mutex.

### As Evidências no Código Fonte

### Evidência 1: A constante dos 5 minutos:

No arquivo cosmos_global_endpoint_manager.go, o SDK crava o tempo de expiração do cache local:

```Go
const (
	defaultRefreshTimeInterval = 5 * time.Minute
)
```

### Evidência 2: O gargalo síncrono (O "Cadeado"):

No mesmo arquivo, a estrutura do globalEndpointManager declara um sync.RWMutex. Quando o relógio bate 5 minutos, a função Update é acionada. Observe o mutex.Lock():

```Go
func (gem *globalEndpointManager) Update(ctx context.Context, forceRefresh bool) error {
	gem.mutex.Lock()
	defer gem.mutex.Unlock()

	// Se outra goroutine já atualizou enquanto essa esperava o Lock, ele sai cedo:
	if !forceRefresh && time.Since(gem.lastUpdateTime) < gem.refreshTimeInterval {
		return nil
	}

	// [O GARGALO SÍNCROno ESTÁ AQUI]
	// Ele dispara uma requisição HTTP síncrona na raiz da conta (GET /)
	accountProperties, err := gem.client.getDatabaseAccount(ctx)
	if err != nil {
		return err
	}

	gem.locationCache.OnDatabaseAccountRead(accountProperties)
	gem.lastUpdateTime = time.Now()
	return nil
}
```

### Evidência 3: O gatilho disparado dentro da sua requisição:

No arquivo cosmos_client.go (ou na esteira de montagem do pipeline HTTP), antes de despachar o seu payload de negócio (um ReadItem ou UpsertItem), o SDK é obrigado a descobrir para qual URL mandar o pacote chamando o endpoint manager:

```Go
func (c *Client) sendRequest(ctx context.Context, req *policy.Request) (*http.Response, error) {
	// ...
	endpoint, err := c.endpointManager.GetEndpoint(ctx) // <-- ELE ENTRA AQUI
	// ...
}
```

Dentro de GetEndpoint(ctx), ele avalia: se (agora - ultima_atualizacao) > 5 min -> chama Update(ctx).

### O que acontece no marco dos 5 minutos sob 500 TPS:
 
- Às 12:00:00.000, a expiração do tempo atinge 300 segundos.
- No milissegundo 12:00:00.001, chegam 15 requisições simultâneas do seu tráfego de 500 TPS.
- A Goroutine #1 entra no Update(), adquire o gem.mutex.Lock() e vai na rede fazer um GET https://sua-conta.documents.azure.com/. Digamos que a rede demore 40ms para responder essa chamada administrativa.
- As Goroutines #2 até a #15 tentam adquirir o mesmo mutex.Lock(). Elas sofrem bloqueio de thread imediato.
- Nos 40ms seguintes, enquanto a Goroutine #1 espera o I/O da rede, chegam mais ~20 novas requisições do seu tráfego. Todas empilham no Mutex.
- Quando a Goroutine #1 solta o cadeado (Unlock), você tem um engarrafamento de dezenas de goroutines sendo acordadas pelo Scheduler do Go simultaneamente para tentar re-adquirir o http.Transport subjacente, causando picos de alocação de memória e esgotamento instantâneo do pool de conexões do http.Client

# Tratativa

## Sequência de Implementação (O Que Fazer)

## Passo 1: Ajustar as Threads no Código Go

No arquivo k8s do Container Sidecar adicione a variavel GOMAXPROCS=4, force o Go a criar mais canais de execução para não ficar refém de apenas 2 threads.

## Passo 2: Blindar o Pod no Kubernetes (deployment.yaml)

Evite que outros Pods vizinhos roubem a CPU da sua máquina reservando recursos mínimos via requests (mantenha o limits em aberto para permitir o comportamento de Burst).

spec:
  containers:
  - name: seu-app-sidecar
    resources:
      requests:
        cpu: "800m"      # Garante quase 1 núcleo exclusivo para o Sidecar aguentar o pico
        memory: "512Mi"

## Passo 3: Avaliar Upgrade de Hardware 

Se o problema diminuir mas persistir, considere mudar o tipo de máquina da Azure para focar em desempenho por núcleo individual (Single-Core):

* Opção 1: Mudar para a série Standard_F2s_v2 (Processadores Intel Xeon de clock alto, 3.4GHz+).
* Opção 2: Mudar para a série Standard_D2ds_v5 (Processadores de geração v5 mais modernos).

## Guia de Monitoramento no Datadog

Monte um dashboard com as métricas abaixo para evidenciar o comportamento do Go 1.26 e do Kubernetes.

| O Que Monitorar (Métrica) | Onde Olhar / Nome no Datadog | Como Interpretar o Gráfico |
|---|---|---|
| Atraso no Agendamento do Go | go_sched_latencies_seconds_bucket ou go.sched.latencies (Filtre pelo P99) | Se houver picos de milissegundos a cada 5 minutos: Comprova o Starvation. Significa que as Goroutines estão prontas, mas esperando a CPU liberar espaço. |
| Estrangulamento Invisível do Kernel | kubernetes.cpu.cfs.throttled.time ou container.cpu.throttled | Se subir de 5 em 5 minutos: Prova que o Linux/Kubernetes está congelando o seu container por picos de uso, mesmo que na média você veja apenas 16% de CPU. |
| Empilhamento de Requisições | go.goroutines | Se o número de Goroutines saltar repentinamente no 5º minuto: Mostra o efeito do bloqueio. Novas requisições entram, mas ficam presas na memória porque o pipeline do Cosmos DB parou. |

## 1. Gráfico: Atraso no Agendamento do Go (Prova Real do Starvation)
Esta query mede o tempo (em segundos) que uma Goroutine fica na fila esperando a CPU liberar espaço para ela rodar. Focamos no percentil 99 (p99) para pegar os picos exatos de travamento.

p99:go.sched.latencies{container_name:seu-app-sidecar}


* Como interpretar: Em funcionamento normal, este gráfico deve ficar próximo de zero (microssundos). Se a cada 5 minutos você vir um pico vertical subindo para a casa dos milissegundos, o Starvation por falta de CPU física está confirmado.

## 2. Gráfico: Estrangulamento de CPU pelo Linux (CFS Throttling)
Esta query mede a quantidade de tempo que o Linux efetivamente "congelou" o seu container por tentar ultrapassar limites invisíveis ou por conta da disputa de hardware no nó do Kubernetes.

sum:kubernetes.cpu.cfs.throttled.time{container_name:seu-app-sidecar} by {pod_name}


* (Nota: Se o Datadog coletar via métricas de container padrão, use esta alternativa: sum:container.cpu.throttled{container_name:seu-app-sidecar} by {pod_name})
* Como interpretar: O valor ideal é zero. Se houver picos repetitivos de 5 em 5 minutos, significa que o kernel do Linux está pausando o seu Sidecar bem na hora em que o Cosmos DB tenta atualizar os metadados.

## 3. Gráfico: Empilhamento de Goroutines (Efeito do Bloqueio)
Esta query monitora a quantidade de threads lógicas (Goroutines) abertas na memória do Go.

sum:go.goroutines{container_name:seu-app-sidecar} by {pod_name}


* Como interpretar: O gráfico deve ser uma linha estável compatível com seus 500 TPS. Se no 5º minuto a linha der um salto agudo para cima (um "degrau"), significa que o pipeline do Cosmos DB travou, e as novas requisições que continuam chegando estão se empilhando na memória sem conseguir resposta.

## 4. Gráfico: Uso de CPU em Millicores (Para correlacionar com os 360m)
Para você comparar visualmente os gráficos anteriores com o consumo real de CPU no mesmo instante do tempo.

sum:kubernetes.cpu.usage.total{container_name:seu-app-sidecar} by {pod_name} / 1000000


* (Nota: Se usar métricas de container clássicas: sum:container.cpu.usage{container_name:seu-app-sidecar} by {pod_name})
* Como interpretar: Você verá a média em torno de 360m. Fique atento para ver se há uma "agulhada" (um pico micro-repentino) para cima exatamente coincidindo com os picos dos gráficos 1 e 2.
