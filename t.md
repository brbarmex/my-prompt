# Devin Scheduler Prompts — BRBARMEX AI Harness

This document defines the prompts to be used by each Devin Scheduler in the BRBARMEX AI Harness.

Each scheduler prompt MUST:

1. Load the correct shared contract.
2. Execute exactly one selected playbook.
3. Respect the contract output format.
4. Read artifacts only from the expected previous stage.
5. Write artifacts into the expected current stage path.
6. Avoid expanding scope beyond the selected playbook.
7. Commit generated artifacts into the harness artifact repository when applicable.

---

# Assumed Repository Structure

```text
.ai/
  contracts/
    00-shared-radar-discovery-contract.md
    01-shared-finding-validation-contract.md
    02-shared-backlog-contract.md
    03-shared-engineering-contract.md
    04-shared-testing-contract.md
    05-shared-review-contract.md
    06-shared-governance-contract.md

  playbooks/
    discovery/
      bug-panic.md
      reliability-resilience.md
      observability.md
      performance.md
      testing-gap.md
      security.md
      maintainability-architecture.md
      cloud-sdk-infra.md
      documentation-operational-readiness.md

    validation/
      validate-findings.md
      deduplicate-findings.md
      eligibility-gate.md

    backlog/
      generate-backlog-items.md
      prioritize-backlog-items.md

    engineering/
      implement-backlog-item.md

    testing/
      validate-implementation.md

    review/
      review-implementation.md

    governance/
      governance-merge-gate.md
      report-results.md

runs/
  <run_group_id>/
    discovery/raw/
    validation/
    backlog/
    engineering/
    testing/
    review/
    governance/
```

---

# Global Devin Scheduler Rules

Use these rules in every scheduler prompt.

```text
You are operating inside the BRBARMEX AI Harness.

You MUST follow the selected shared contract before executing the selected playbook.

You MUST NOT skip the contract.
You MUST NOT execute more than one selected playbook unless explicitly instructed.
You MUST NOT change application code unless the selected scheduler is an engineering scheduler.
You MUST NOT create backlog directly from discovery findings unless the backlog contract is selected and the input has passed validation.
You MUST NOT approve your own implementation unless the selected scheduler is review or governance and required prior evidence exists.

Use the current timestamp to create or reuse a run_group_id.
If a run_group_id already exists in the scheduler input, reuse it.
If no run_group_id exists, create one using yyyyMMdd-HHmmss.

All generated artifacts MUST include:
- run_group_id
- execution_id
- agent name
- target repository
- target branch
- target commit SHA
- created_at timestamp

When writing artifacts, generate both:
- JSON artifact
- Markdown report

Prefer fewer high-quality outputs over many weak outputs.
Do not invent evidence.
Do not fabricate test results.
Do not fabricate command execution.
Do not mark work as passed without evidence.
```

---
