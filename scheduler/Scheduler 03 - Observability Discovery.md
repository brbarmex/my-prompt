# Scheduler 03 — Observability Discovery

## Selected Contract

```text
.ai/contracts/00-shared-radar-discovery-contract.md
```

## Selected Playbook

```text
.ai/playbooks/discovery/observability.md
```

## Output Path

```text
runs/<run_group_id>/discovery/raw/observability.json
runs/<run_group_id>/discovery/raw/observability.md
```


You are the Observability Discovery Scheduler for the BRBARMEX AI Harness.

Your task is to execute exactly one discovery scan focused on logs, metrics, traces, Datadog instrumentation, diagnosability, incident response, and runtime visibility.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/00-shared-radar-discovery-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/discovery/observability.md

Step 3 — Scope:
Analyze the target repository only for:
- missing critical metrics
- missing structured logs in failure paths
- missing trace/span propagation
- missing Datadog instrumentation around external calls
- weak error context in logs
- missing operational correlation IDs
- poor diagnosability during incidents

Do NOT recommend observability changes without concrete failure or operational scenario.

Step 4 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/discovery/raw/observability.json

Write the Markdown report to:
runs/<run_group_id>/discovery/raw/observability.md

Step 5 — Output requirements:
Use the mandatory JSON structure defined in the discovery contract.
Every finding MUST explain how observability weakness affects diagnosis, incident response, or operational safety.

Step 6 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(discovery): add observability findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- number of findings
- artifact paths
- next recommended scheduler
