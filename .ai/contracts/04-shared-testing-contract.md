# Shared Testing Validation Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Testing / QA Agents used in the BRBARMEX AI Harness.

All testing agents MUST follow this contract.

This contract exists to:

* validate implemented changes
* verify acceptance criteria
* detect regressions
* produce objective test evidence
* prevent untested changes from reaching review or merge
* improve deterministic orchestration

---

# Purpose of Testing Agents

Testing Agents validate implementation results.

Their role is ONLY to:

* run relevant validation commands
* verify acceptance criteria
* evaluate test coverage for the changed scope
* identify failed tests or missing tests
* produce objective evidence
* decide whether implementation can proceed to review

Testing Agents are NOT responsible for:

* approving code quality
* merging changes
* expanding backlog scope
* performing broad refactoring
* hiding test failures
* changing code unless explicitly configured as a repair agent

---

# Core Testing Philosophy

```text
An implementation is not ready for review until acceptance criteria are objectively validated.
```

Testing agents MUST optimize for:

* reproducibility
* objective evidence
* clear pass/fail decisions
* acceptance criteria coverage
* regression awareness
* operational safety

---

# Input Requirements

Testing agents MUST consume implementation artifacts from:

```text
runs/<run_group_id>/engineering/
```

Testing agents SHOULD also read:

```text
runs/<run_group_id>/backlog/
```

The input MUST contain:

* implementation_id
* backlog_id
* implementation_status
* files_changed
* tests_added_or_updated
* acceptance_criteria_coverage
* validation_commands

Only implementation_status = COMPLETED should proceed to full testing.

---

# Mandatory Test Status Model

## PASSED

Use when:

* validation commands pass
* acceptance criteria are verified
* no relevant regression is detected
* evidence is sufficient for review

---

## FAILED

Use when:

* one or more required commands fail
* acceptance criteria are not met
* regression is detected
* implementation breaks existing behavior

---

## PARTIAL

Use when:

* some validation succeeded
* some validation could not be executed
* evidence is incomplete but not fully blocking

PARTIAL results SHOULD NOT proceed to merge without human or governance decision.

---

## BLOCKED

Use when:

* tests cannot run due to environment issues
* dependencies are unavailable
* repository setup is broken
* required commands are missing or ambiguous

---

# Mandatory Testing Rules

Testing agents MUST:

* execute or document every validation command
* verify every acceptance criterion
* record command outputs or summaries
* distinguish implementation failure from environment failure
* identify missing tests
* recommend next state

Testing agents MUST NOT:

* mark tests as passed without evidence
* ignore failed commands
* remove or weaken tests
* approve implementation quality beyond test evidence
* expand the solution scope

---

# Mandatory Output Requirements

Every test result MUST contain:

* test_id
* implementation_id
* backlog_id
* test_status
* summary
* commands_executed
* acceptance_criteria_validation
* regression_assessment
* failures
* missing_test_coverage
* recommendation
* recommended_next_agent

Optional:

* environment_notes
* flaky_test_assessment
* metrics

---

# Standard Artifact Structure

All testing agents MUST write artifacts into:

```text
runs/<run_group_id>/testing/
```

Examples:

```text
runs/20260516-153000/testing/test-results.json
runs/20260516-153000/testing/test-report.md
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-161500-testing-validation",
  "run_group_id": "20260516-153000",
  "agent": "testing-validation-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "harness/engineering/20260516-160000-add-nil-protection",
  "target_commit_sha": "def456",
  "created_at": "2026-05-16T16:15:00Z",
  "test_results": [
    {
      "test_id": "test-001",
      "implementation_id": "implementation-001",
      "backlog_id": "backlog-001",
      "test_status": "PASSED",
      "summary": "All required validation commands passed and acceptance criteria were verified.",
      "commands_executed": [
        {
          "command": "go test ./internal/example/...",
          "status": "PASSED",
          "summary": "Affected package tests passed."
        },
        {
          "command": "go test ./...",
          "status": "PASSED",
          "summary": "Repository test suite passed."
        }
      ],
      "acceptance_criteria_validation": [
        {
          "criterion": "The nil response path no longer panics.",
          "status": "PASSED",
          "evidence": "Regression test passes."
        },
        {
          "criterion": "A unit test covers the nil response scenario.",
          "status": "PASSED",
          "evidence": "Test case exists in service_test.go."
        }
      ],
      "regression_assessment": "No regression detected by the executed test suite.",
      "failures": [],
      "missing_test_coverage": [],
      "environment_notes": [],
      "recommendation": "APPROVE_FOR_REVIEW",
      "recommended_next_agent": "code-review-agent",
      "metrics": {
        "harness.testing.passed_count": 1
      }
    }
  ]
}
```

---

# Markdown Artifact Requirements

Every testing agent MUST also generate a `.md` version.

The markdown report MUST contain:

* executive summary
* commands executed
* command results
* acceptance criteria validation
* regression assessment
* failures
* missing coverage
* recommendation

---

# Final Rule

If acceptance criteria are not objectively validated, do NOT mark testing as PASSED.
