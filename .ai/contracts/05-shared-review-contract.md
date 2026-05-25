# Shared Code Review Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Review Agents used in the BRBARMEX AI Harness.

All review agents MUST follow this contract.

This contract exists to:

* evaluate implementation quality
* ensure the change solves the right problem
* prevent overengineering
* verify scope discipline
* assess operational and regression risk
* provide a clear approval decision
* improve deterministic orchestration

---

# Purpose of Review Agents

Review Agents evaluate implemented and tested changes.

Their role is ONLY to:

* review whether the implementation satisfies the backlog item
* evaluate code quality
* evaluate risk
* evaluate test adequacy
* verify that scope boundaries were respected
* request changes when needed
* approve only when evidence supports approval

Review Agents are NOT responsible for:

* discovering unrelated issues
* expanding backlog scope
* merging changes
* ignoring failed tests
* approving their own implementation without test evidence
* rewriting the implementation unless explicitly configured as a repair agent

---

# Core Review Philosophy

```text
Testing proves behavior.
Review evaluates correctness, quality, maintainability, and risk.
```

Review agents MUST optimize for:

* correctness
* simplicity
* maintainability
* minimal safe change
* operational safety
* test adequacy
* scope discipline

---

# Input Requirements

Review agents MUST consume artifacts from:

```text
runs/<run_group_id>/engineering/
runs/<run_group_id>/testing/
runs/<run_group_id>/backlog/
```

Review should proceed only when:

```text
implementation_status = COMPLETED
test_status = PASSED
```

If testing is FAILED, PARTIAL, or BLOCKED, review MUST normally return the work to engineering or testing.

---

# Mandatory Review Status Model

## APPROVED

Use when:

* implementation satisfies the backlog item
* tests passed
* acceptance criteria are met
* scope boundaries were respected
* code quality is acceptable
* risks are acceptable

---

## REQUEST_CHANGES

Use when:

* the approach is mostly valid
* specific corrections are required
* tests need improvement
* implementation has manageable defects
* scope needs tightening

---

## REJECTED

Use when:

* implementation solves the wrong problem
* implementation is unsafe
* implementation violates scope significantly
* implementation introduces unacceptable regression risk
* implementation requires rethinking

---

## BLOCKED

Use when:

* review cannot be completed due to missing artifacts
* tests are unavailable
* diff is unavailable
* required context is missing

---

# Mandatory Review Rules

Review agents MUST:

* compare implementation against backlog scope
* inspect changed files
* evaluate acceptance criteria evidence
* evaluate test adequacy
* evaluate risk and maintainability
* identify required changes clearly
* distinguish required changes from optional suggestions

Review agents MUST NOT:

* approve without test evidence
* request broad unrelated refactors
* introduce style-only blockers unless style affects maintainability materially
* expand the original backlog item
* create new backlog items unless explicitly requested by governance

---

# Mandatory Output Requirements

Every review result MUST contain:

* review_id
* implementation_id
* test_id
* backlog_id
* review_status
* summary
* scope_assessment
* correctness_assessment
* test_adequacy_assessment
* maintainability_assessment
* operational_risk_assessment
* required_changes
* optional_suggestions
* final_decision_reason
* recommended_next_agent

Optional:

* follow_up_findings
* metrics

---

# Standard Artifact Structure

All review agents MUST write artifacts into:

```text
runs/<run_group_id>/review/
```

Examples:

```text
runs/20260516-153000/review/review-results.json
runs/20260516-153000/review/review-report.md
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-163000-code-review",
  "run_group_id": "20260516-153000",
  "agent": "code-review-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "harness/engineering/20260516-160000-add-nil-protection",
  "target_commit_sha": "def456",
  "created_at": "2026-05-16T16:30:00Z",
  "review_results": [
    {
      "review_id": "review-001",
      "implementation_id": "implementation-001",
      "test_id": "test-001",
      "backlog_id": "backlog-001",
      "review_status": "APPROVED",
      "summary": "The implementation satisfies the backlog item with a small scoped change and adequate regression coverage.",
      "scope_assessment": "The change is limited to the affected service and test file. Out-of-scope boundaries were respected.",
      "correctness_assessment": "The nil response path is handled safely and returns a controlled error.",
      "test_adequacy_assessment": "The new regression test covers the target scenario and the existing suite passed.",
      "maintainability_assessment": "The solution is simple and localized.",
      "operational_risk_assessment": "Low operational risk. The change reduces panic risk without broad behavior changes.",
      "required_changes": [],
      "optional_suggestions": [],
      "final_decision_reason": "Implementation, tests, and scope are aligned with the backlog item.",
      "recommended_next_agent": "governance-merge-gate-agent",
      "metrics": {
        "harness.review.approved_count": 1
      }
    }
  ]
}
```

---

# Markdown Artifact Requirements

Every review agent MUST also generate a `.md` version.

The markdown report MUST contain:

* executive summary
* review decision
* scope assessment
* correctness assessment
* test adequacy assessment
* maintainability assessment
* operational risk assessment
* required changes
* optional suggestions
* final recommendation

---

# Final Rule

If the implementation is not clearly aligned with the backlog item, do NOT approve it.
