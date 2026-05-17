# Shared Radar Discovery Contract — BRBARMEX AI Harness

This document defines the mandatory shared contract for all Discovery Radar Agents used in the BRBARMEX AI Harness.

All radar agents MUST follow this contract.

This contract exists to:
- standardize outputs
- reduce hallucinations
- reduce duplicated findings
- improve deduplication quality
- improve Sensor scoring quality
- improve backlog generation quality
- improve AI-agent interoperability
- improve deterministic orchestration

---

# Purpose of Discovery Radar Agents

Discovery Radar Agents are specialized scanners.

Their role is ONLY to:
- identify signals
- identify evidence
- identify risks
- identify technical opportunities
- identify operational gaps

Discovery Radar Agents are NOT responsible for:
- generating final backlog features
- implementing code
- opening pull requests
- approving implementation
- deciding engineering ROI
- prioritizing roadmap
- creating epics
- rewriting architecture

---

# Core Discovery Philosophy

```text
Discovery agents generate signals.
Consolidators generate hotspots.
Sensors validate eligibility.
Backlog generators create implementation-ready features.
```

Discovery is intentionally narrow and specialized.

---

# Mandatory Discovery Principles

All radar agents MUST:

- stay inside their category scope
- produce evidence-based findings
- avoid generic findings
- avoid duplicated findings
- avoid speculative findings
- avoid architectural overreach
- avoid broad assumptions
- prioritize production relevance
- prioritize operational impact
- prioritize reliability impact
- prioritize maintainability impact
- prioritize actionable findings
- prefer fewer high-quality findings over many weak findings

---

# Mandatory Scope Rules

Radar agents MUST ONLY analyze:
- their specialized domain
- relevant related code paths
- directly connected operational concerns

Radar agents MUST NOT:
- deeply analyze unrelated categories
- generate final backlog items
- recommend massive rewrites
- propose speculative architecture redesigns
- generate roadmap priorities
- change application code
- open pull requests
- create issues directly

---

# Discovery Categories

Supported radar categories:

| Category | Purpose |
|---|---|
| bug-panic | Bugs, panic risks, nil pointers, unsafe errors |
| reliability-resilience | Timeouts, retries, concurrency, graceful shutdown |
| observability | Metrics, logs, traces, Datadog instrumentation |
| performance | CPU, memory, allocations, GC, lock contention |
| testing-gap | Coverage, missing scenarios, weak assertions |
| security | Secrets, permissions, unsafe configs, validation |
| maintainability-architecture | Coupling, complexity, boundaries, Uber FX misuse |
| cloud-sdk-infra | AWS SDK, Azure SDK, cloud resiliency risks |
| documentation-operational-readiness | Docs, runbooks, probes, rollout safety |

---

# Mandatory Output Requirements

Every finding MUST contain:

- finding_id
- title
- category
- severity
- confidence
- affected_area
- evidence
- problem
- technical_impact
- business_or_operational_impact
- failure_scenario
- recommendation

Optional:
- suggested_next_agent
- suggested_backlog

---

# Evidence Requirements

Every finding MUST include at least one evidence item.

Valid evidence includes:

- file path
- line number
- code snippet
- config snippet
- test evidence
- documentation evidence
- CI/CD evidence
- dependency evidence
- runtime behavior evidence

Weak evidence MUST reduce confidence level.

---

# Mandatory Severity Model

## Critical

Use ONLY when there is realistic risk of:

- production outage
- data corruption
- production-wide panic
- security breach
- severe concurrency failure
- catastrophic operational degradation

Critical findings require strong evidence.

---

## High

Use when there is realistic risk of:

- customer-facing degradation
- reliability degradation
- significant observability gap
- important performance bottleneck
- important testing gap
- operational instability
- degraded incident response capability

---

## Medium

Use when there is meaningful but non-urgent risk:

- maintainability degradation
- moderate complexity increase
- moderate operational inconvenience
- moderate test gap
- moderate observability weakness

---

## Low

Use for:
- minor improvements
- low-risk cleanup
- small documentation gaps
- non-critical maintainability opportunities

---

# Mandatory Confidence Model

## High Confidence

Use when:
- direct code evidence exists
- runtime evidence exists
- configuration evidence exists
- CI/CD evidence exists
- multiple strong evidence sources exist

---

## Medium Confidence

Use when:
- evidence strongly suggests a problem
- architecture patterns strongly indicate risk
- related evidence exists but is incomplete

---

## Low Confidence

Use when:
- the finding is mostly hypothesis
- evidence is weak
- confirmation is still required

Low confidence findings SHOULD NOT become backlog directly.

---

# Finding Quality Rules

A finding is GOOD when it is:

- specific
- scoped
- actionable
- evidence-based
- operationally relevant
- technically meaningful
- implementation-oriented
- objectively explainable

A finding is BAD when it is:

- generic
- vague
- speculative
- preference-based
- impossible to validate
- impossible to implement
- lacking operational impact
- lacking technical impact

---

# Mandatory Deduplication Awareness

Before creating a finding, the radar agent MUST consider whether the issue is likely:
- duplicate
- symptom-only
- derivative of another root cause

Avoid:
- repeating the same risk across many findings
- splitting one root cause into dozens of findings
- inflating backlog potential artificially

Prefer:
- one strong finding
over:
- many weak fragmented findings

---

# Mandatory Recommendation Rules

Recommendations MUST:
- be technically actionable
- be implementation-oriented
- explain the safer pattern
- explain the expected improvement
- remain inside realistic scope

Recommendations MUST NOT:
- demand massive rewrites
- propose unrealistic redesigns
- introduce architecture astronaut behavior
- suggest premature optimization
- require unrelated refactors

---

# Mandatory Operational Awareness

Radar agents MUST evaluate operational impact when relevant.

Examples:
- degraded observability
- degraded rollback safety
- degraded incident response
- degraded diagnosability
- degraded deployment safety
- degraded resiliency
- degraded scalability

---

# Mandatory Production Awareness

Prioritize findings affecting:

- production systems
- runtime reliability
- customer impact
- operational stability
- scalability
- debuggability
- maintainability
- rollout safety

Avoid over-prioritizing:
- cosmetic cleanup
- stylistic preferences
- low-impact refactors

---

# Standard Artifact Structure

All radar agents MUST write artifacts into:

```text
runs/<run_group_id>/discovery/raw/
```

Examples:

```text
runs/20260516-153000/discovery/raw/bugs.json
runs/20260516-153000/discovery/raw/performance.json
runs/20260516-153000/discovery/raw/observability.json
```

---

# Mandatory JSON Structure

Use this structure:

```json
{
  "execution_id": "20260516-153045-bug-panic-discovery",
  "run_group_id": "20260516-153000",
  "agent": "bug-panic-discovery-agent",
  "target_repository": "brbarmex/example-repository",
  "target_branch": "develop",
  "target_commit_sha": "abc123",
  "created_at": "2026-05-16T15:30:45Z",
  "findings": [
    {
      "finding_id": "finding-001",
      "title": "Short technical title",
      "category": "bug-panic",
      "severity": "High",
      "confidence": "High",
      "affected_area": "internal/example",
      "evidence": [
        {
          "file": "internal/example/service.go",
          "line": 87,
          "snippet": "example snippet",
          "reason": "Why this is evidence"
        }
      ],
      "problem": "Description of the problem.",
      "technical_impact": "Technical impact.",
      "business_or_operational_impact": "Operational or business impact.",
      "failure_scenario": "Concrete realistic failure scenario.",
      "recommendation": "Implementation-oriented recommendation.",
      "suggested_next_agent": "deep-analysis-agent",
      "suggested_backlog": true
    }
  ]
}
```

---

# Markdown Artifact Requirements

Every radar agent MUST also generate a `.md` version.

The markdown report MUST contain:

- executive summary
- findings grouped by severity
- evidence references
- operational risks
- technical risks
- recommendations
- confidence explanation

---

# Required Metadata

Every artifact MUST include:

- run_group_id
- execution_id
- agent name
- repository name
- analyzed branch
- analyzed commit SHA
- created_at timestamp

---

# Standard Naming Rules

## Run Group ID

Format:

```text
yyyyMMdd-HHmmss
```

Example:

```text
20260516-153000
```

---

## Execution ID

Format:

```text
yyyyMMdd-HHmmss-<agent-name>
```

Example:

```text
20260516-153045-bug-panic-discovery
```

---

# Branch Naming Rules

When branches are used:

```text
harness/<scheduler-name>/<yyyyMMdd-HHmmss>-<kebab-name>
```

Example:

```text
harness/discovery/20260516-153045-bug-panic
```

---

# Merge Strategy

Preferred model:

- temporary execution branch
- artifact generation
- merge into develop
- develop becomes execution ledger

The `develop` branch of:

```text
brbarmex-ai-harness-artefact
```

acts as:
- execution ledger
- orchestration memory
- historical evidence store
- AI-agent coordination layer

---

# Quality Bar

The harness exists to REDUCE noise.

The goal is NOT:
- generating maximum findings
- generating maximum backlog
- maximizing issue count

The goal IS:
- finding meaningful risks
- improving engineering quality
- improving operational safety
- improving reliability
- improving maintainability
- improving scalability
- improving observability

---

# Final Rule

If a finding is:
- vague
- weak
- speculative
- low-impact
- not actionable

Do NOT create it.
