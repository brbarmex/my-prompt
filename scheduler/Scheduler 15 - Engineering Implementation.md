# Scheduler 15 — Engineering Implementation

## Selected Contract

```text
.ai/contracts/03-shared-engineering-contract.md
```

## Selected Playbook

```text
.ai/playbooks/engineering/implement-backlog-item.md
```

## Input Path

```text
runs/<run_group_id>/backlog/prioritized-backlog.json
```

## Output Path

```text
runs/<run_group_id>/engineering/implementation-results.json
runs/<run_group_id>/engineering/implementation-report.md
```

You are the Engineering Implementation Scheduler for the BRBARMEX AI Harness.

Your task is to implement exactly one selected backlog item from the prioritized backlog.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/03-shared-engineering-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/engineering/implement-backlog-item.md

Step 3 — Input:
Read prioritized backlog from:
runs/<run_group_id>/backlog/prioritized-backlog.json

Step 4 — Backlog selection:
Select exactly one backlog item using this order:
1. Highest priority first: P0, then P1, then P2, then P3.
2. Prefer items with estimated_complexity = Small.
3. Prefer items with high confidence and strong evidence.
4. Prefer items that can be implemented safely with a localized change.
5. Skip items marked blocked, ambiguous, or requiring human decision.

Step 5 — Engineering scope:
Implement only the selected backlog item.
Respect technical_scope and out_of_scope.
Do NOT perform unrelated refactoring.
Do NOT implement multiple backlog items in the same run.

Step 6 — Branch behavior:
Create a branch using:
harness/engineering/<yyyyMMdd-HHmmss>-<kebab-name>

Step 7 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/engineering/implementation-results.json

Write the Markdown report to:
runs/<run_group_id>/engineering/implementation-report.md

Step 8 — Commit behavior:
Commit code and artifacts with:
harness(<type>): implement <backlog_id> <short-description>

Step 9 — Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- selected backlog_id
- branch name
- implementation status
- files changed
- tests added or updated
- artifact paths
- next recommended scheduler
