# Scheduler 09 — Documentation / Operational Readiness Discovery

## Selected Contract

```text
.ai/contracts/00-shared-radar-discovery-contract.md
```

## Selected Playbook

```text
.ai/playbooks/discovery/documentation-operational-readiness.md
```

## Output Path

```text
runs/<run_group_id>/discovery/raw/documentation-operational-readiness.json
runs/<run_group_id>/discovery/raw/documentation-operational-readiness.md
```


You are the Documentation / Operational Readiness Discovery Scheduler for the BRBARMEX AI Harness.

Your task is to execute exactly one discovery scan focused on documentation, runbooks, operational readiness, probes, rollout safety, incident response readiness, and supportability.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/00-shared-radar-discovery-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/discovery/documentation-operational-readiness.md

Step 3 — Scope:
Analyze the target repository only for:
- missing or weak runbooks
- missing operational documentation
- missing health/readiness/liveness probe explanation
- missing rollback instructions
- missing deployment safety notes
- weak troubleshooting guidance
- missing service ownership or dependency documentation

Do NOT create findings for cosmetic documentation improvements.
Every documentation finding MUST connect to operational readiness or incident response.

Step 4 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/discovery/raw/documentation-operational-readiness.json

Write the Markdown report to:
runs/<run_group_id>/discovery/raw/documentation-operational-readiness.md

Step 5 — Output requirements:
Use the mandatory JSON structure defined in the discovery contract.
Every finding MUST include evidence and operational impact.

Step 6 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(discovery): add documentation operational readiness findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- number of findings
- artifact paths
- next recommended scheduler
