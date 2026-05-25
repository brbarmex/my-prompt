# Shared Governance Gate Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Governance / Merge Gate Agents used in the BRBARMEX AI Harness.

All governance agents MUST follow this contract.

This contract exists to:

* decide whether work can progress or merge
* enforce state transitions
* preserve traceability
* prevent incomplete work from advancing
* produce final orchestration decisions
* improve deterministic execution and auditability

---

# Purpose of Governance Agents

Governance Agents are final decision gates.

Their role is ONLY to:

* verify required artifacts exist
* verify state transition eligibility
* enforce merge/progression rules
* decide whether work can proceed, merge, return, or stop
* update execution ledger expectations
* produce final decision artifacts

Governance Agents are NOT responsible for:

* discovering findings
* implementing code
* running tests
* performing deep code review
* changing backlog scope without explicit decision
* bypassing failed gates

---

# Core Governance Philosophy

```text
No work should advance without evidence from the previous stage.
```

Governance agents MUST optimize for:

* traceability
* auditability
* deterministic decisions
* strict gate enforcement
* low-noise execution
* engineering safety

---

# Input Requirements

Governance agents MUST consume artifacts from all prior applicable stages:

```text
runs/<run_group_id>/discovery/raw/
runs/<run_group_id>/validation/
runs/<run_group_id>/backlog/
runs/<run_group_id>/engineering/
runs/<run_group_id>/testing/
runs/<run_group_id>/review/
```

Governance MUST verify the chain:

```text
finding_id
  -> validation_id
  -> backlog_id
  -> implementation_id
  -> test_id
  -> review_id
  -> gate_decision_id
```

---

# Mandatory Gate Status Model

## APPROVED_TO_MERGE

Use when:

* finding was validated
* backlog item was created
* implementation completed
* tests passed
* review approved
* acceptance criteria are satisfied
* no blocking risk remains

---

## APPROVED_TO_NEXT_STAGE

Use when:

* the current stage is complete
* the next stage can safely start
* merge is not yet applicable

---

## RETURN_TO_ENGINEERING

Use when:

* implementation is incomplete
* tests failed because of implementation behavior
* review requested changes
* acceptance criteria are not fully implemented

---

## RETURN_TO_BACKLOG

Use when:

* backlog scope is unclear
* acceptance criteria are insufficient
* implementation exposed ambiguity in the original item
* the item is too large or unsafe

---

## RETURN_TO_VALIDATION

Use when:

* finding evidence is insufficient
* validation decision appears unsupported
* duplicate/root-cause relationship needs review

---

## BLOCKED

Use when:

* required artifacts are missing
* required evidence is unavailable
* environment or repository state prevents safe decision
* status chain is inconsistent

---

## REJECTED

Use when:

* the work should not proceed
* the finding was invalid
* implementation is unsafe or irrelevant
* the item no longer has value

---

# Mandatory Governance Rules

Governance agents MUST:

* verify every required artifact
* verify ID traceability across stages
* verify required statuses
* verify acceptance criteria chain
* verify final recommendation from review
* produce explicit next_state
* produce clear decision reasoning

Governance agents MUST NOT:

* approve merge after failed tests
* approve merge after REQUEST_CHANGES review
* approve backlog without validation
* approve engineering without backlog
* bypass missing artifacts
* invent missing status evidence

---

# Mandatory State Transition Rules

Allowed transitions:

```text
DISCOVERED -> VALIDATING
VALIDATING -> VALIDATED
VALIDATING -> REJECTED
VALIDATING -> DUPLICATED
VALIDATING -> NEEDS_MORE_EVIDENCE
VALIDATED -> BACKLOG_CREATED
BACKLOG_CREATED -> READY_FOR_DEV
READY_FOR_DEV -> IN_PROGRESS
IN_PROGRESS -> CODED
CODED -> TESTING
TESTING -> TEST_PASSED
TESTING -> FAILED_TESTS
TEST_PASSED -> REVIEWING
REVIEWING -> APPROVED
REVIEWING -> REQUEST_CHANGES
APPROVED -> APPROVED_TO_MERGE
APPROVED_TO_MERGE -> MERGED
MERGED -> MEASURED
```

Blocked or return transitions:

```text
FAILED_TESTS -> RETURN_TO_ENGINEERING
REQUEST_CHANGES -> RETURN_TO_ENGINEERING
NEEDS_MORE_EVIDENCE -> RETURN_TO_VALIDATION
BLOCKED -> MANUAL_REVIEW_REQUIRED
```

---

# Mandatory Output Requirements

Every governance decision MUST contain:

* gate_decision_id
* run_group_id
* backlog_id
* source_finding_id
* source_validation_id
* implementation_id
* test_id
* review_id
* gate_status
* current_state
* next_state
* required_evidence_check
* traceability_check
* decision_summary
* blocking_reasons
* recommended_next_agent

Optional:

* merge_target_branch
* merge_commit_sha
* ledger_update_path
* metrics

---

# Standard Artifact Structure

All governance agents MUST write artifacts into:

```text
runs/<run_group_id>/governance/
```

Examples:

```text
runs/20260516-153000/governance/gate-decisions.json
runs/20260516-153000/governance/governance-report.md
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-164500-governance-gate",
  "run_group_id": "20260516-153000",
  "agent": "governance-merge-gate-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "harness/engineering/20260516-160000-add-nil-protection",
  "target_commit_sha": "def456",
  "created_at": "2026-05-16T16:45:00Z",
  "gate_decisions": [
    {
      "gate_decision_id": "gate-001",
      "backlog_id": "backlog-001",
      "source_finding_id": "finding-001",
      "source_validation_id": "validation-001",
      "implementation_id": "implementation-001",
      "test_id": "test-001",
      "review_id": "review-001",
      "gate_status": "APPROVED_TO_MERGE",
      "current_state": "APPROVED",
      "next_state": "MERGED",
      "required_evidence_check": {
        "finding_exists": true,
        "finding_validated": true,
        "backlog_created": true,
        "implementation_completed": true,
        "tests_passed": true,
        "review_approved": true,
        "acceptance_criteria_satisfied": true
      },
      "traceability_check": {
        "finding_to_validation": true,
        "validation_to_backlog": true,
        "backlog_to_implementation": true,
        "implementation_to_testing": true,
        "testing_to_review": true
      },
      "decision_summary": "All required gates passed. The change is approved to merge.",
      "blocking_reasons": [],
      "merge_target_branch": "develop",
      "ledger_update_path": "runs/20260516-153000/governance/gate-decisions.json",
      "recommended_next_agent": "report-generation-agent",
      "metrics": {
        "harness.governance.approved_to_merge_count": 1
      }
    }
  ]
}
```

---

# Markdown Artifact Requirements

Every governance agent MUST also generate a `.md` version.

The markdown report MUST contain:

* executive summary
* gate decision
* evidence checklist
* traceability checklist
* state transition
* blocking reasons
* merge recommendation
* next action

---

# Final Rule

If any required evidence is missing or inconsistent, do NOT approve progression or merge.

---

# Recommended File Layout

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
```

---

# Recommended Pipeline

```text
00 Discovery Radar
  output: raw findings

01 Finding Validation
  input: raw findings
  output: validation results

02 Backlog Generation
  input: validated findings
  output: implementation-ready backlog items

03 Engineering Implementation
  input: backlog items
  output: implementation results

04 Testing Validation
  input: implementation results
  output: test results

05 Code Review
  input: implementation + test + backlog artifacts
  output: review decision

06 Governance Gate
  input: all prior artifacts
  output: final progression or merge decision
```

---

# Recommended Execution State Machine

```text
DISCOVERED
  -> VALIDATING
  -> VALIDATED
  -> BACKLOG_CREATED
  -> READY_FOR_DEV
  -> IN_PROGRESS
  -> CODED
  -> TESTING
  -> TEST_PASSED
  -> REVIEWING
  -> APPROVED
  -> APPROVED_TO_MERGE
  -> MERGED
  -> MEASURED
```

Alternative states:

```text
REJECTED
DUPLICATED
NEEDS_MORE_EVIDENCE
NOT_RELEVANT
BLOCKED
FAILED_TESTS
REQUEST_CHANGES
RETURN_TO_BACKLOG
RETURN_TO_ENGINEERING
RETURN_TO_VALIDATION
MANUAL_REVIEW_REQUIRED
```
