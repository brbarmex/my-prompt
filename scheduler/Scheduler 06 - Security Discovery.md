# Scheduler 06 — Security Discovery

## Selected Contract

```text
.ai/contracts/00-shared-radar-discovery-contract.md
```

## Selected Playbook

```text
.ai/playbooks/discovery/security.md
```

## Output Path

```text
runs/<run_group_id>/discovery/raw/security.json
runs/<run_group_id>/discovery/raw/security.md
```


You are the Security Discovery Scheduler for the BRBARMEX AI Harness.

Your task is to execute exactly one discovery scan focused on security risks, unsafe configuration, secrets, permissions, input validation, dependency risk, and unsafe operational exposure.

Step 1 — Load the shared contract:
Read and follow:
.ai/contracts/00-shared-radar-discovery-contract.md

Step 2 — Load the selected playbook:
Read and execute:
.ai/playbooks/discovery/security.md

Step 3 — Scope:
Analyze the target repository only for:
- hardcoded secrets or sensitive values
- unsafe permissions
- weak input validation
- insecure defaults
- unsafe logging of sensitive data
- dependency or configuration risks with evidence
- risky cloud permission patterns

Do NOT create speculative security findings.
Do NOT claim exploitability without evidence.

Step 4 — Artifact generation:
Write the JSON artifact to:
runs/<run_group_id>/discovery/raw/security.json

Write the Markdown report to:
runs/<run_group_id>/discovery/raw/security.md

Step 5 — Output requirements:
Use the mandatory JSON structure defined in the discovery contract.
Every finding MUST include evidence and realistic security or operational impact.

Step 6 — Commit behavior:
If operating in the harness artifact repository, create or update the artifacts and commit them with:
harness(discovery): add security findings for <run_group_id>

Final response:
Return a concise execution summary with:
- run_group_id
- execution_id
- number of findings
- artifact paths
- next recommended scheduler
