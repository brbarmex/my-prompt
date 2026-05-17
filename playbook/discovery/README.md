# BRBARMEX AI Harness Playbook

This folder contains the prompt playbooks used by the BRBARMEX AI Harness.

The goal of this harness is to orchestrate AI agents through a controlled software engineering pipeline capable of:

- discovering technical debt
- identifying bugs and fragility
- finding reliability risks
- detecting observability gaps
- detecting testing gaps
- identifying performance bottlenecks
- identifying security risks
- generating backlog candidates
- validating backlog relevance through a Sensor
- creating implementation-ready backlog features
- implementing approved features
- reviewing pull requests

---

# Core Pipeline

```text
Discovery Radar Agents
        ↓
Raw Hotspot Signals
        ↓
Hotspot Consolidator
        ↓
Deduplication & Correlation
        ↓
Sensor / Guard-Rail
        ↓
Backlog Feature Generator
        ↓
Implementation Agent
        ↓
Code Review Agent
        ↓
CI/CD Validation
```

---

# Core Principle

```text
Discovery agents generate signals.
Consolidator generates hotspots.
Sensor decides eligibility.
Backlog generator creates implementation-ready issues.
Implementation agent writes code.
Review agent validates the PR.
```

---

# Repository Naming

Use the BRBARMEX naming convention across all prompts, artifacts, branches, issues, and reports.

Use:

```text
brbarmex-ai-harness-artefact
brbarmex
```

---

# Playbook Files

## Discovery Radar Agents

| File | Purpose |
|---|---|
| `00-shared-radar-discovery-contract.md` | Shared contract, output schema, rules, and guardrails for all radar agents |
| `01-bug-panic-discovery-agent.md` | Finds bugs, nil pointer risks, panic risks, unsafe error handling, and edge-case failures |
| `02-reliability-resilience-discovery-agent.md` | Finds reliability, timeout, retry, idempotency, goroutine, race condition, and graceful shutdown risks |
| `03-observability-discovery-agent.md` | Finds gaps in metrics, logs, traces, Datadog instrumentation, correlation IDs, and alertability |
| `04-performance-discovery-agent.md` | Finds CPU, memory, allocation, GC, serialization, lock contention, and throughput risks |
| `05-testing-gap-discovery-agent.md` | Finds low coverage, missing scenarios, weak assertions, missing system/e2e tests, and test fragility |
| `06-security-discovery-agent.md` | Finds secrets, unsafe configs, input validation gaps, insecure cloud usage, and permission risks |
| `07-maintainability-architecture-discovery-agent.md` | Finds complexity, coupling, package boundary violations, architecture debt, and Uber FX misuse |
| `08-cloud-sdk-infra-discovery-agent.md` | Finds AWS SDK, Azure SDK, cloud coupling, retry config, timeout config, and resource cleanup risks |
| `09-documentation-operational-readiness-discovery-agent.md` | Finds stale documentation, missing runbooks, poor readiness/liveness checks, and operational gaps |

---

# Non-Discovery Agents

Future playbooks may include:

| File | Purpose |
|---|---|
| `10-hotspot-consolidator-agent.md` | Consolidates raw radar outputs into normalized hotspots |
| `11-dedup-correlation-agent.md` | Removes duplicates and groups findings by root cause |
| `12-sensor-eligibility-gate-agent.md` | Scores candidates from 0 to 100 and approves only score >= 90 |
| `13-backlog-feature-generator-agent.md` | Creates implementation-ready backlog features |
| `14-implementation-agent.md` | Implements approved backlog items |
| `15-code-review-agent.md` | Reviews PRs against backlog requirements and changed code only |

---

# Artifact Repository

All harness execution artifacts should be persisted in:

```text
brbarmex-ai-harness-artefact
```

Recommended structure:

```text
runs/
  <run_group_id>/
    metadata.json
    discovery/
      raw/
        bugs.json
        reliability.json
        observability.json
        performance.json
        testing.json
        security.json
        maintainability-architecture.json
        cloud-sdk-infra.json
        documentation-operational-readiness.json
      consolidated/
        hotspot-map.json
        hotspot-map.md
    correlation/
      candidate-features.json
      candidate-features.md
    sensor/
      sensor-results.json
      sensor-results.md
    backlog/
      backlog-features/
```

---

# Run Identification

Each execution must include:

```text
run_group_id
execution_id
agent_name
target_repository
target_branch
target_commit_sha
created_at
```

Recommended format:

```text
run_group_id = yyyyMMdd-HHmmss
execution_id = yyyyMMdd-HHmmss-<agent-name>
```

Example:

```text
run_group_id = 20260516-153000
execution_id = 20260516-153045-bug-panic-discovery
```

---

# Discovery Agent Rules

Discovery agents MUST:

- analyze only their specialized category
- produce evidence-based findings
- include exact file paths when available
- include exact line numbers when available
- include snippets when safe and useful
- classify severity and confidence
- avoid generic findings
- avoid speculative claims
- avoid duplicated findings
- avoid creating backlog issues
- avoid implementing code
- avoid opening pull requests
- avoid changing application repositories

Discovery agents MUST NOT:

- create issues
- create PRs
- write code
- modify the application repository
- generate final backlog features
- approve implementation
- inflate severity without evidence
- produce vague recommendations
- analyze unrelated categories deeply

---

# Severity Model

Use this severity model across all agents.

## Critical

Use when there is realistic risk of:

- production outage
- data corruption
- security breach
- panic in production-critical path
- severe concurrency failure
- irreversible operational impact

## High

Use when there is realistic risk of:

- degraded reliability
- customer-impacting failures
- poor incident diagnosability
- significant performance degradation
- important missing tests
- important observability gap

## Medium

Use when there is meaningful but non-urgent risk:

- maintainability degradation
- moderate complexity
- moderate test gap
- moderate performance inefficiency
- operational inconvenience

## Low

Use when the issue is useful but not urgent:

- minor cleanup
- small documentation improvement
- minor naming or structure issue
- low-risk improvement opportunity

---

# Confidence Model

Use this confidence model across all agents.

## High

The finding has direct evidence in code, tests, docs, configuration, or CI/CD files.

## Medium

The finding is strongly suggested by available evidence but may require confirmation.

## Low

The finding is a hypothesis and must not become backlog without more evidence.

---

# Minimum Evidence Requirement

Every finding must include at least one of:

- code path
- file path
- line number
- test gap evidence
- configuration evidence
- documentation evidence
- dependency usage evidence
- CI/CD evidence
- observable runtime risk

If evidence is weak, mark confidence as `Low`.

---

# Standard Finding Schema

All discovery agents should produce findings using this JSON shape:

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
      "category": "bug",
      "severity": "High",
      "confidence": "High",
      "affected_area": "internal/example",
      "evidence": [
        {
          "file": "internal/example/service.go",
          "line": 87,
          "snippet": "example code snippet",
          "reason": "Why this is evidence"
        }
      ],
      "problem": "Clear description of the problem.",
      "technical_impact": "Technical impact.",
      "business_or_operational_impact": "Business or operational impact.",
      "failure_scenario": "Concrete failure scenario.",
      "recommendation": "Implementation-oriented recommendation.",
      "suggested_next_agent": "deep-analysis-agent",
      "suggested_backlog": true
    }
  ]
}
```

---

# Output Format

Each radar agent should produce:

```text
runs/<run_group_id>/discovery/raw/<category>.json
runs/<run_group_id>/discovery/raw/<category>.md
```

Example:

```text
runs/20260516-153000/discovery/raw/bugs.json
runs/20260516-153000/discovery/raw/bugs.md
```

---

# Quality Bar

A finding is good when it is:

- specific
- evidence-based
- actionable
- technically relevant
- operationally meaningful
- scoped
- deduplicable
- suitable for later Sensor scoring

A finding is bad when it is:

- generic
- speculative
- cosmetic
- impossible to verify
- missing evidence
- missing impact
- too broad
- not actionable
- just a personal preference

---

# Final Reminder

The harness must reduce noise, not create noise.

Prefer fewer high-quality findings over many weak findings.
