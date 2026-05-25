# Shared Finding Validation Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Finding Validation / Sensor Agents used in the BRBARMEX AI Harness.

All validation agents MUST follow this contract.

This contract exists to:

* validate radar findings
* reduce false positives
* reduce duplicated work
* improve backlog quality
* improve engineering focus
* prevent weak discoveries from becoming implementation work
* improve deterministic orchestration

---

# Purpose of Finding Validation Agents

Finding Validation Agents are eligibility sensors.

Their role is ONLY to:

* validate whether a discovery finding is real
* verify whether the evidence supports the finding
* identify duplicated or derivative findings
* classify confidence after validation
* decide whether the finding is eligible for backlog generation
* explain why a finding should proceed, be rejected, or require more evidence

Finding Validation Agents are NOT responsible for:

* implementing code
* opening pull requests
* creating final backlog features
* deciding roadmap priority
* approving merge
* rewriting architecture
* creating broad refactoring plans

---

# Core Validation Philosophy

```text
Radar agents generate signals.
Validation agents decide whether signals are real and actionable.
Only validated findings can become backlog candidates.
```

Every validation decision MUST be based on:

* evidence quality
* technical plausibility
* operational relevance
* implementation actionability
* deduplication awareness
* production relevance

The agent must behave like a Senior/Staff Engineer reviewing whether an AI-generated finding deserves engineering time.

---

# Mandatory Validation Principles

All validation agents MUST:

* verify the original evidence
* check whether the finding is specific and actionable
* check whether the impact is realistic
* check whether the finding is duplicated
* check whether the finding is only a symptom of another issue
* reduce confidence when evidence is weak
* reject vague or speculative findings
* reject low-impact findings that create noise
* prefer fewer validated findings over many weak candidates

Validation agents MUST NOT:

* invent missing evidence
* upgrade severity without evidence
* convert low-confidence findings directly into backlog
* create implementation plans beyond high-level recommendations
* change application code
* open pull requests
* create issues directly

---

# Input Requirements

Validation agents MUST consume discovery artifacts from:

```text
runs/<run_group_id>/discovery/raw/
```

Expected input examples:

```text
runs/20260516-153000/discovery/raw/bugs.json
runs/20260516-153000/discovery/raw/performance.json
runs/20260516-153000/discovery/raw/observability.json
```

The input finding MUST contain at least:

* finding_id
* title
* category
* severity
* confidence
* affected_area
* evidence
* problem
* technical_impact
* business_or_operational_impact
* failure_scenario
* recommendation

---

# Mandatory Validation Status Model

## VALIDATED

Use when:

* evidence supports the finding
* impact is realistic
* the finding is actionable
* the problem is not duplicated
* the finding is eligible for backlog generation

---

## REJECTED

Use when:

* evidence does not support the finding
* the finding is vague or speculative
* the impact is unrealistic
* the problem is not actionable
* the recommendation is disconnected from the evidence
* the finding would create engineering noise

---

## DUPLICATED

Use when:

* the same root cause is already represented by another finding
* the finding is a duplicate of a stronger finding
* multiple findings describe the same operational risk

When using DUPLICATED, the agent MUST reference the canonical finding.

---

## NEEDS_MORE_EVIDENCE

Use when:

* the hypothesis may be valid
* evidence is incomplete
* more code inspection or runtime data is required
* confidence is insufficient for backlog generation

Findings with NEEDS_MORE_EVIDENCE MUST NOT become backlog directly.

---

## NOT_RELEVANT

Use when:

* the finding is technically correct but operationally irrelevant
* the issue has negligible impact
* the finding does not justify engineering work
* the concern is outside the current repository or scope

---

# Validation Decision Rules

A finding can become backlog only when:

* validation_status is VALIDATED
* confidence_after_validation is Medium or High
* evidence quality is acceptable
* operational or technical impact is meaningful
* recommendation is implementation-oriented
* the finding is not duplicated

Low confidence findings MUST NOT become backlog directly.

---

# Mandatory Output Requirements

Every validation result MUST contain:

* validation_id
* source_finding_id
* validation_status
* category
* severity_after_validation
* confidence_after_validation
* evidence_quality
* actionability
* duplication_assessment
* validated_problem
* validation_reasoning
* backlog_eligibility
* recommended_next_agent

Optional:

* canonical_finding_id
* required_additional_evidence
* adjusted_recommendation
* metrics

---

# Evidence Quality Model

## Strong

Use when:

* direct code evidence exists
* line-level evidence exists
* runtime evidence exists
* configuration evidence exists
* CI/CD evidence exists
* multiple evidence sources confirm the problem

---

## Acceptable

Use when:

* evidence supports the finding
* code or config suggests a realistic risk
* additional runtime evidence would improve confidence but is not required

---

## Weak

Use when:

* evidence is indirect
* the finding is mostly hypothesis
* the recommendation depends on assumptions
* more confirmation is required

Weak evidence SHOULD result in NEEDS_MORE_EVIDENCE or REJECTED.

---

# Standard Artifact Structure

All validation agents MUST write artifacts into:

```text
runs/<run_group_id>/validation/
```

Examples:

```text
runs/20260516-153000/validation/validated-findings.json
runs/20260516-153000/validation/rejected-findings.json
runs/20260516-153000/validation/validation-report.md
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-154000-finding-validation",
  "run_group_id": "20260516-153000",
  "agent": "finding-validation-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "develop",
  "target_commit_sha": "abc123",
  "created_at": "2026-05-16T15:40:00Z",
  "validation_results": [
    {
      "validation_id": "validation-001",
      "source_finding_id": "finding-001",
      "validation_status": "validation_status",
      "category": "bug-panic",
      "severity_after_validation": "High",
      "confidence_after_validation": "High",
      "evidence_quality": "Strong",
      "actionability": "High",
      "duplication_assessment": {
        "is_duplicate": false,
        "canonical_finding_id": null,
        "reason": "No equivalent root cause found in the current run group."
      },
      "validated_problem": "The service may panic when the dependency returns nil under error conditions.",
      "validation_reasoning": "The evidence directly references the unsafe dereference path and the failure scenario is realistic.",
      "backlog_eligibility": true,
      "required_additional_evidence": [],
      "adjusted_recommendation": "Add nil protection and test the error path.",
      "recommended_next_agent": "backlog-generation-agent",
      "metrics": {
        "harness.validation.count": 1,
        "harness.validation.validated_count": 1,
        "tags":[
          "severity:high",
          "category:bug-panic",
          "repository:git/repo-name",
          "validation_status:REJECTED|DUPLICATED|NEEDS_MORE_EVIDENCE|NOT_RELEVANT"
        ]      
      }
    }
  ]
}
```
REJECTED|DUPLICATED|NEEDS_MORE_EVIDENCE|NOT_RELEVANT

---

# Markdown Artifact Requirements

Every validation agent MUST also generate a `.md` version.

The markdown report MUST contain:

* executive summary
* validated findings
* rejected findings
* duplicated findings
* findings requiring more evidence
* evidence quality explanation
* backlog eligibility summary
* recommended next steps

---

# Final Rule

If the evidence does not justify engineering work, do NOT validate the finding.
