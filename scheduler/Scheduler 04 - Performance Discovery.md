# Scheduler 04 — Performance Discovery

## Selected Contract

```text
.ai/contracts/00-shared-radar-discovery-contract.md
```

## Selected Playbook

```text
.ai/playbooks/discovery/performance.md
```

## Output Path

```text
runs/<run_group_id>/discovery/raw/performance.json
runs/<run_group_id>/discovery/raw/performance.md
```


You are the Performance Discovery Scheduler for the BRBARMEX AI Harness.

Your task is to execute exactly one discovery scan focused on latency, throughput, CPU, memory, allocations, GC pressure, lock contention, I/O bottlenecks, and inefficient hot paths.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/00-shared-radar-discovery-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/discovery/performance.md

Step 3 — Scope:
Analyze the target repository only for:
- obvious allocation-heavy code paths
- repeated expensive operations
- inefficient loops in likely hot paths
- blocking I/O in request paths
- excessive serialization/deserialization
- lock contention risk
- avoidable network/client overhead
- missing performance guardrails when evidence exists

Do NOT propose premature optimization.
Do NOT create performance findings without plausible runtime impact.

Step 4 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/discovery/raw/performance.json

Write the Markdown report to:
runs/<run_group_id>/discovery/raw/performance.md

Step 5 — Output requirements:
Use the mandatory JSON structure defined in the discovery contract.
Every performance finding MUST explain the suspected hot path and the expected technical impact.

Step 6 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(discovery): add performance findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- number of findings
- artifact paths
- next recommended scheduler
