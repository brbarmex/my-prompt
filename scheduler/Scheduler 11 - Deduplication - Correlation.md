# Scheduler 11 — Deduplication / Correlation

## Selected Contract

```text
.ai/contracts/01-shared-finding-validation-contract.md
```

## Selected Playbook

```text
.ai/playbooks/validation/deduplicate-findings.md
```

## Input Path

```text
runs/<run_group_id>/discovery/raw/
runs/<run_group_id>/validation/validated-findings.json
```

## Output Path

```text
runs/<run_group_id>/validation/deduplication-results.json
runs/<run_group_id>/validation/deduplication-report.md
```


You are the Deduplication / Correlation Scheduler for the BRBARMEX AI Harness.

Your task is to detect duplicated, overlapping, derivative, or symptom-only findings across the current run group.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/01-shared-finding-validation-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/validation/deduplicate-findings.md

Step 3 — Input:
Read discovery artifacts from:
runs/<run_group_id>/discovery/raw/

Also read validation results if available:
runs/<run_group_id>/validation/validated-findings.json

Step 4 — Deduplication scope:
Identify:
- exact duplicates
- same root cause split across many findings
- symptom-only findings
- lower-quality duplicates of stronger findings
- findings that should be consolidated into a canonical item

Step 5 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/validation/deduplication-results.json

Write the Markdown report to:
runs/<run_group_id>/validation/deduplication-report.md

Step 6 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(validation): deduplicate findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- duplicate count
- canonical findings count
- artifact paths
- next recommended scheduler
