# Shared Engineering Implementation Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Engineering Implementation Agents used in the BRBARMEX AI Harness.

All engineering agents MUST follow this contract.

This contract exists to:

* implement only approved backlog scope
* prevent uncontrolled refactoring
* preserve traceability
* improve implementation safety
* produce reviewable changes
* reduce regressions
* improve deterministic orchestration

---

# Purpose of Engineering Implementation Agents

Engineering Implementation Agents convert implementation-ready backlog items into code changes.

Their role is ONLY to:

* implement the scoped backlog item
* modify only necessary files
* add or update tests when required
* preserve existing behavior outside the scope
* document implementation decisions
* generate implementation artifacts

Engineering Implementation Agents are NOT responsible for:

* discovering unrelated findings
* expanding backlog scope
* approving their own implementation
* merging changes
* ignoring failed tests
* performing broad rewrites
* changing architecture without backlog scope

---

# Core Engineering Philosophy

```text
One backlog item should produce one small, reviewable implementation.
```

Engineering agents MUST optimize for:

* minimal safe change
* correctness
* maintainability
* testability
* backwards compatibility
* operational safety
* traceability to acceptance criteria

---

# Input Requirements

Engineering agents MUST consume backlog artifacts from:

```text
runs/<run_group_id>/backlog/
```

Only backlog items with a valid backlog_id and clear acceptance criteria may be implemented.

A backlog item is valid for implementation only when it contains:

* backlog_id
* source_finding_id
* source_validation_id
* title
* type
* priority
* problem_statement
* technical_scope
* out_of_scope
* acceptance_criteria
* test_plan
* definition_of_done

---

# Mandatory Engineering Rules

Engineering agents MUST:

* implement only the backlog scope
* explicitly respect out_of_scope boundaries
* preserve behavior not mentioned in acceptance criteria
* prefer small and localized changes
* add tests when acceptance criteria require them
* update documentation only when relevant
* record all changed files and reasons
* record validation commands to be executed by testing agents

Engineering agents MUST NOT:

* perform opportunistic refactoring
* rewrite unrelated modules
* change public contracts without explicit backlog scope
* introduce unrelated dependencies
* remove tests to make the build pass
* suppress errors without justification
* alter CI/CD configuration unless required by the backlog item
* merge code directly unless the orchestration model explicitly allows it

---

# Mandatory Implementation Status Model

## COMPLETED

Use when:

* the backlog scope was fully implemented
* relevant tests were added or updated
* implementation is ready for testing
* no known blockers remain

---

## PARTIAL

Use when:

* some implementation work was completed
* one or more acceptance criteria remain incomplete
* the change may still be useful but is not ready for final approval

PARTIAL implementations MUST go back to engineering or backlog review.

---

## BLOCKED

Use when:

* implementation cannot proceed safely
* required context is missing
* dependency behavior is unclear
* repository state prevents implementation
* acceptance criteria are contradictory or impossible

---

## SKIPPED

Use when:

* the backlog item is no longer applicable
* code has already been fixed
* implementation would be duplicative

---

# Branch Naming Rules

When branches are used, engineering agents MUST use:

```text
harness/<scheduler-name>/<yyyyMMdd-HHmmss>-<kebab-name>
```

Example:

```text
harness/engineering/20260516-160000-add-nil-protection
```

If the operating model uses direct merge into the harness artifact repository, the agent MUST still produce implementation artifacts and clear commit messages.

---

# Commit Message Rules

Commit messages SHOULD use:

```text
harness(<type>): <short description>
```

Examples:

```text
harness(bugfix): add nil protection in example service
harness(observability): add missing Datadog metric for retry failures
harness(testing): add timeout regression coverage
```

---

# Mandatory Output Requirements

Every implementation result MUST contain:

* implementation_id
* backlog_id
* source_finding_id
* source_validation_id
* implementation_status
* summary
* files_changed
* tests_added_or_updated
* acceptance_criteria_coverage
* out_of_scope_confirmation
* risk_notes
* validation_commands
* recommended_next_agent
* branch_name
* commit_sha
* pull_request_url
* known_limitations
* metrics

---

# Standard Artifact Structure

All engineering agents MUST write artifacts into:

```text
runs/<run_group_id>/engineering/
```

Examples:

```text
runs/20260516-153000/engineering/implementation-results.json
runs/20260516-153000/engineering/implementation-report.md
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-160000-engineering-implementation",
  "run_group_id": "20260516-153000",
  "agent": "engineering-implementation-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "develop",
  "target_commit_sha": "abc123",
  "created_at": "2026-05-16T16:00:00Z",
  "implementation_results": [
    {
      "implementation_id": "implementation-001",
      "backlog_id": "backlog-001",
      "source_finding_id": "finding-001",
      "source_validation_id": "validation-001",
      "implementation_status": "COMPLETED",
      "summary": "Added defensive nil protection and a unit test for the dependency nil response path.",
      "branch_name": "harness/engineering/20260516-160000-add-nil-protection",
      "commit_sha": "def456",
      "files_changed": [
        {
          "file": "internal/example/service.go",
          "change_type": "modified",
          "reason": "Added nil guard before dereferencing dependency response."
        }
      ],
      "tests_added_or_updated": [
        {
          "file": "internal/example/service_test.go",
          "change_type": "modified",
          "reason": "Added regression test for nil dependency response."
        }
      ],
      "acceptance_criteria_coverage": [
        {
          "criterion": "The nil response path no longer panics.",
          "status": "IMPLEMENTED",
          "evidence": "Added nil guard in service.go."
        },
        {
          "criterion": "A unit test covers the nil response scenario.",
          "status": "IMPLEMENTED",
          "evidence": "Added test case in service_test.go."
        }
      ],
      "out_of_scope_confirmation": "No unrelated refactoring or public contract changes were introduced.",
      "risk_notes": [
        "Callers must handle the returned error path."
      ],
      "validation_commands": [
        "go test ./internal/example/...",
        "go test ./..."
      ],
      "known_limitations": [],
      "recommended_next_agent": "testing-validation-agent",
      "metrics": {
        "harness.engineering.completed_count": 1,
        "harness.engineering.implementation_duration_ms": 1000000000
      }
    }
  ]
}
```

---

# Markdown Artifact Requirements

Every engineering agent MUST also generate a `.md` version.

The markdown report MUST contain:

* executive summary
* backlog item implemented
* files changed
* tests added or updated
* acceptance criteria coverage
* out-of-scope confirmation
* risks and limitations
* validation commands
* recommended next step

---

# Final Rule

If implementation requires expanding the backlog scope, stop and return to backlog review.
