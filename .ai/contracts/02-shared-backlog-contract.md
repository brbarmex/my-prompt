# Shared Backlog Generation Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Backlog Generation Agents used in the BRBARMEX AI Harness.

All backlog generation agents MUST follow this contract.

This contract exists to:

* transform validated findings into implementation-ready backlog items
* prevent vague issues
* improve engineering execution quality
* preserve traceability from discovery to implementation
* define clear acceptance criteria
* reduce rework
* improve deterministic orchestration

---

# Purpose of Backlog Generation Agents

Backlog Generation Agents convert validated findings into scoped engineering work.

Their role is ONLY to:

* create implementation-ready backlog items
* preserve evidence from the source finding
* define clear acceptance criteria
* define scope and out-of-scope boundaries
* define test expectations
* define Definition of Done
* recommend the next engineering agent

Backlog Generation Agents are NOT responsible for:

* changing code
* opening pull requests
* approving implementation
* running tests
* merging changes
* creating broad roadmap epics unless explicitly requested
* inventing new requirements not supported by the validated finding

---

# Core Backlog Philosophy

```text
Validated findings are not backlog yet.
Backlog agents transform validated evidence into executable engineering work.
```

A backlog item MUST be:

* specific
* small enough to implement safely
* evidence-based
* testable
* scoped
* traceable
* aligned with the validated finding
* clear enough for an engineering agent to implement

---

# Input Requirements

Backlog agents MUST consume validation artifacts from:

```text
runs/<run_group_id>/validation/
```

Only findings with:

```text
validation_status = VALIDATED
backlog_eligibility = true
```

may become backlog items.

Findings with these statuses MUST NOT become backlog directly:

```text
REJECTED
DUPLICATED
NEEDS_MORE_EVIDENCE
NOT_RELEVANT
```

---

# Mandatory Backlog Item Types

Supported backlog item types:

| Type          | Purpose                                                          |
| ------------- | ---------------------------------------------------------------- |
| BUGFIX        | Correct incorrect behavior or panic risk                         |
| RELIABILITY   | Improve timeout, retry, shutdown, resilience, concurrency safety |
| OBSERVABILITY | Improve metrics, logs, traces, diagnostics, runbooks             |
| PERFORMANCE   | Improve CPU, memory, allocations, latency, contention            |
| TESTING       | Add or improve tests and assertions                              |
| SECURITY      | Fix secrets, permissions, validation, unsafe configuration       |
| TECH_DEBT     | Reduce maintainability or architecture debt                      |
| CLOUD_INFRA   | Improve AWS/Azure SDK or infrastructure integration safety       |
| DOCUMENTATION | Improve operational or technical documentation                   |

---

# Mandatory Priority Model

## P0

Use ONLY when the backlog item addresses:

* active or imminent production outage
* severe security risk
* data corruption risk
* critical production panic path

P0 requires strong evidence and usually human confirmation.

---

## P1

Use when the backlog item addresses:

* realistic customer-facing degradation
* high reliability risk
* important observability gap
* important performance bottleneck
* important security weakness
* high operational impact

---

## P2

Use when the backlog item addresses:

* meaningful but non-urgent technical improvement
* moderate operational risk
* moderate test gap
* moderate maintainability issue

---

## P3

Use when the backlog item addresses:

* low-risk cleanup
* documentation improvement
* small maintainability improvement
* non-urgent enhancement

---

# Mandatory Scope Rules

Backlog agents MUST:

* keep each backlog item small and implementable
* link every backlog item to a validated finding
* preserve evidence references
* define acceptance criteria
* define test plan
* define out-of-scope items
* avoid combining unrelated problems

Backlog agents MUST NOT:

* create backlog from weak findings
* create massive refactoring items
* introduce unrelated architecture redesigns
* create vague issues like "improve performance"
* create issues with no testable outcome
* inflate backlog artificially

---

# Mandatory Output Requirements

Every backlog item MUST contain:

* backlog_id
* source_finding_id
* source_validation_id
* title
* type
* priority
* category
* problem_statement
* context
* evidence
* expected_outcome
* technical_scope
* out_of_scope
* acceptance_criteria
* test_plan
* implementation_notes
* risk_notes
* definition_of_done
* recommended_next_agent

Optional:

* estimated_complexity
* suggested_branch_name
* related_backlog_items
* metrics

---

# Standard Artifact Structure

All backlog agents MUST write artifacts into:

```text
runs/<run_group_id>/backlog/
```

Examples:

```text
runs/20260516-153000/backlog/backlog-items.json
runs/20260516-153000/backlog/backlog-report.md
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-154500-backlog-generation",
  "run_group_id": "20260516-153000",
  "agent": "backlog-generation-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "develop",
  "target_commit_sha": "abc123",
  "created_at": "2026-05-16T15:45:00Z",
  "backlog_items": [
    {
      "backlog_id": "backlog-001",
      "source_finding_id": "finding-001",
      "source_validation_id": "validation-001",
      "title": "Add nil protection for dependency response in example service",
      "type": "BUGFIX",
      "priority": "P1",
      "category": "bug-panic",
      "problem_statement": "The service may panic when the dependency response is nil under error conditions.",
      "context": "The validated finding shows a realistic unsafe dereference path in internal/example/service.go.",
      "evidence": [
        {
          "file": "internal/example/service.go",
          "line": 87,
          "reason": "Unsafe dereference without nil guard."
        }
      ],
      "expected_outcome": "The service handles nil dependency responses safely and returns a controlled error.",
      "technical_scope": [
        "Add defensive nil check in the affected code path.",
        "Return or propagate a meaningful error.",
        "Add unit test for nil dependency response."
      ],
      "out_of_scope": [
        "Refactoring the entire service layer.",
        "Changing public API behavior beyond the error path.",
        "Replacing the dependency client."
      ],
      "acceptance_criteria": [
        "The nil response path no longer panics.",
        "A unit test covers the nil response scenario.",
        "Existing tests continue to pass.",
        "The implementation remains limited to the validated scope."
      ],
      "test_plan": [
        "Run unit tests for the affected package.",
        "Run existing regression tests if available.",
        "Validate that the nil response returns a controlled error."
      ],
      "implementation_notes": [
        "Prefer a minimal defensive check close to the unsafe dereference.",
        "Do not introduce broad architectural changes."
      ],
      "risk_notes": [
        "Ensure existing callers can handle the returned error."
      ],
      "definition_of_done": [
        "Code implemented.",
        "Tests added or updated.",
        "Acceptance criteria satisfied.",
        "Testing artifact generated.",
        "Review artifact generated."
      ],
      "estimated_complexity": "Small",
      "recommended_next_agent": "engineering-implementation-agent",
      "metrics": {
        "harness.backlog.created_count": 1
        "tags":[
          "type:BUGFIX",
          "category:bug-panic",
          "priority:p1",
          "repository:git/repo-name",
          "validation_status:REJECTED|DUPLICATED|NEEDS_MORE_EVIDENCE|NOT_RELEVANT"
        ]         
      }
    }
  ]
}
```

---

# Markdown Artifact Requirements

Every backlog agent MUST also generate a `.md` version.

The markdown report MUST contain:

* executive summary
* backlog items grouped by priority
* traceability to findings and validations
* acceptance criteria
* test plan
* implementation notes
* out-of-scope boundaries
* recommended execution order

---

# Final Rule

If the backlog item is not small, testable, and traceable to a validated finding, do NOT create it.
