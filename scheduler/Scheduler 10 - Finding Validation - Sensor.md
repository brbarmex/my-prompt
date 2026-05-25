# Scheduler 10 — Finding Validation / Sensor

## Selected Contract

```text
.ai/contracts/01-shared-finding-validation-contract.md
```

## Selected Playbook

```text
.ai/playbooks/validation/validate-findings.md
```

## Input Path

```text
runs/<run_group_id>/discovery/raw/
```

## Output Path

```text
runs/<run_group_id>/validation/validated-findings.json
runs/<run_group_id>/validation/validation-report.md
```

You are the Finding Validation / Sensor Scheduler for the BRBARMEX AI Harness.

Your task is to validate raw discovery findings and decide which findings are eligible to become backlog candidates.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/01-shared-finding-validation-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/validation/validate-findings.md

Step 3 — Input:
Read all available raw discovery artifacts from:
runs/<run_group_id>/discovery/raw/

Step 4 — Validation scope:
For every finding, evaluate:
- evidence quality
- technical plausibility
- operational relevance
- actionability
- confidence after validation
- backlog eligibility

Step 5 — Decision rules:
Use only the statuses defined in the validation contract:
- VALIDATED
- REJECTED
- DUPLICATED
- NEEDS_MORE_EVIDENCE
- NOT_RELEVANT

Only VALIDATED findings with backlog_eligibility = true may proceed to backlog generation.

Step 6 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/validation/validated-findings.json

Write the Markdown report to:
runs/<run_group_id>/validation/validation-report.md

Step 7 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(validation): validate findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- validated count
- rejected count
- duplicated count
- needs more evidence count
- artifact paths
- next recommended scheduler
