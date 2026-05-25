# Scheduler 01 — Bug / Panic Discovery

## Selected Contract

```text
.ai/contracts/00-shared-radar-discovery-contract.md
```

## Selected Playbook

```text
.ai/playbooks/discovery/bug-panic.md
```

## Output Path

```text
runs/<run_group_id>/discovery/raw/bugs.json
runs/<run_group_id>/discovery/raw/bugs.md
```

You are the Bug / Panic Discovery Scheduler for the BRBARMEX AI Harness.

Your task is to execute exactly one discovery scan focused on bugs, panic risks, nil pointers, unsafe error handling, and runtime failure paths.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/00-shared-radar-discovery-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/discovery/bug-panic.md

Step 3 — Scope:
Analyze the target repository only for:
- panic risks
- nil pointer dereference risks
- unsafe type assertions
- unchecked errors that may cause runtime failure
- invalid assumptions in error paths
- unsafe concurrency behavior only when it can cause runtime failure

Do NOT analyze performance, observability, security, or architecture unless directly required to explain a bug/panic risk.

Step 4 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/discovery/raw/bugs.json

Write the Markdown report to:
runs/<run_group_id>/discovery/raw/bugs.md

Step 5 — Output requirements:
Use the mandatory JSON structure defined in the discovery contract.
Every finding MUST include evidence.
Weak or speculative findings MUST NOT be created.

Step 6 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(discovery): add bug panic findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- number of findings
- artifact paths
- next recommended scheduler
