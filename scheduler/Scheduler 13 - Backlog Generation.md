# Scheduler 13 — Backlog Generation

## Selected Contract

```text
.ai/contracts/02-shared-backlog-contract.md
```

## Selected Playbook

```text
.ai/playbooks/backlog/generate-backlog-items.md
```

## Input Path

```text
runs/<run_group_id>/validation/eligible-findings.json
```

## Output Path

```text
runs/<run_group_id>/backlog/backlog-items.json
runs/<run_group_id>/backlog/backlog-report.md
```

You are the Backlog Generation Scheduler for the BRBARMEX AI Harness.

Your task is to transform eligible validated findings into implementation-ready backlog items.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/02-shared-backlog-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/backlog/generate-backlog-items.md

Step 3 — Input:
Read only eligible findings from:
runs/<run_group_id>/validation/eligible-findings.json

Step 4 — Backlog generation scope:
For each eligible finding, create a backlog item that includes:
- problem statement
- evidence
- expected outcome
- technical scope
- out of scope
- acceptance criteria
- test plan
- implementation notes
- risk notes
- definition of done

Step 5 — Rules:
Do NOT create backlog from rejected, duplicated, low-confidence, or non-eligible findings.
Do NOT create large refactoring items.
Do NOT combine unrelated findings into one backlog item unless they share the same root cause.

Step 6 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/backlog/backlog-items.json

Write the Markdown report to:
runs/<run_group_id>/backlog/backlog-report.md

Step 7 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(backlog): generate backlog items for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- backlog items created
- artifact paths
- next recommended scheduler
