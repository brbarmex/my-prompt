# AI Harness Engineer Architecture

## Overview

This repository implements an **AI Harness Engineering Architecture** designed to continuously discover, validate, prioritize and resolve technical opportunities inside a software project.

The harness acts as an autonomous engineering system composed of:

- Orchestrators
- Radars
- Sensors
- Validators
- Backlog generators
- Implementation agents
- Review agents

The objective is to create a **self-improving software engineering pipeline** capable of:

- Detecting bugs
- Identifying technical debt
- Discovering performance bottlenecks
- Finding observability gaps
- Detecting architecture problems
- Generating implementation backlogs
- Creating pull requests
- Reviewing code
- Continuously increasing system reliability

---

# What is a Harness?

A Harness is an orchestration layer responsible for coordinating specialized AI agents that operate over a software system.

Instead of using a single generic AI agent, the harness divides responsibilities into specialized domains.

Example:

| Role | Responsibility |
|---|---|
| Radar | Discover opportunities/problems |
| Sensor | Validate if discovery is actionable |
| Correlation Engine | Remove duplicates and correlate findings |
| Prioritizer | Determine engineering priority |
| Backlog Generator | Convert findings into implementation tasks |
| Implementation Agent | Execute changes |
| Review Agent | Validate implementation quality |
| Orchestrator | Control execution flow |

This architecture enables:

- Scalability
- Determinism
- Reliability
- Parallel execution
- Better prompt specialization
- Lower hallucination risk
- Better context isolation

---

# High Level Architecture

```text
                         ┌──────────────────────┐
                         │ Scheduler / Control  │
                         │ Orchestrator Agent   │
                         └──────────┬───────────┘
                                    │
                ┌───────────────────┴────────────────────┐
                │                                        │
        ┌───────▼────────┐                      ┌────────▼────────┐
        │ Discovery      │                      │ Shared Radar     │
        │ Radars         │                      │ Contract         │
        └───────┬────────┘                      └──────────────────┘
                │
     ┌──────────┼───────────────────────────────────────────────┐
     │          │           │            │          │           │
     ▼          ▼           ▼            ▼          ▼           ▼
   Bugs    Reliability  Performance  Security  Testing  Architecture
     │
     └──────────────────────────────┬─────────────────────────────┘
                                    ▼
                     ┌────────────────────────┐
                     │ Dedup / Correlation    │
                     └──────────┬─────────────┘
                                ▼
                     ┌────────────────────────┐
                     │ Root Cause Analysis    │
                     └──────────┬─────────────┘
                                ▼
                     ┌────────────────────────┐
                     │ Prioritization         │
                     └──────────┬─────────────┘
                                ▼
                     ┌────────────────────────┐
                     │ Eligibility Gate       │
                     │ (Sensor)               │
                     └──────────┬─────────────┘
                                ▼
                     ┌────────────────────────┐
                     │ Backlog Generator      │
                     └──────────┬─────────────┘
                                ▼
                     ┌────────────────────────┐
                     │ Implementation Agent   │
                     └──────────┬─────────────┘
                                ▼
                     ┌────────────────────────┐
                     │ Code Review Agent      │
                     └──────────┬─────────────┘
                                ▼
                           CI/CD Pipeline
```

---

# Execution Philosophy

The architecture follows the principle:

> One session = One responsibility

Each scheduler/session must execute only one playbook.

This creates:

- Stateless execution
- Deterministic behavior
- Easier retries
- Better observability
- Better traceability
- Lower prompt contamination
- Better scalability

---

# Core Concepts

## 1. Orchestrator

The orchestrator controls the lifecycle of the harness.

It does NOT execute engineering work directly.

Responsibilities:

- Validate pipeline state
- Validate artifacts
- Trigger next stage
- Control dependencies
- Prevent invalid execution order
- Manage branch/session lifecycle

Main file:

```text
18-scheduler-orchestrator-agent.md
```

---

## 2. Discovery Radars

Radars are specialized discovery agents.

Their responsibility is ONLY to detect opportunities.

They never:

- Implement fixes
- Create PRs
- Modify code
- Prioritize
- Review

Each radar focuses on a single engineering domain.

### Shared Discovery Contract

All discovery radars share:

```text
00-shared-radar-discovery-contract.md
```

### Discovery Radar Playbooks

| Playbook | Responsibility |
|---|---|
| 01-bug-panic-discovery-agent.md | Runtime bugs, panics, exceptions |
| 02-reliability-resilience-discovery-agent.md | Resilience and reliability |
| 03-observability-discovery-agent.md | Logging, tracing, metrics |
| 04-performance-discovery-agent.md | CPU, memory, latency, throughput |
| 05-testing-gap-discovery-agent.md | Missing tests and validation |
| 06-security-discovery-agent.md | Security vulnerabilities |
| 07-maintainability-architecture-discovery-agent.md | Architecture and maintainability |
| 08-cloud-sdk-infra-discovery-agent.md | Cloud and infrastructure |
| 09-documentation-operational-readiness-discovery-agent.md | Documentation and operational readiness |

---

## 3. Correlation Engine

After discovery, findings are consolidated.

Responsibilities:

- Remove duplicates
- Merge correlated findings
- Detect cascading problems
- Consolidate engineering hotspots

Playbook:

```text
11-dedup-correlation-agent.md
```

---

## 4. Root Cause Analysis

This stage investigates WHY the issue exists.

Responsibilities:

- Identify root cause
- Detect systemic problems
- Analyze architecture patterns
- Detect hidden coupling

Playbook:

```text
12-root-cause-analysis-agent.md
```

---

## 5. Prioritization

Determines engineering priority.

Responsibilities:

- Estimate impact
- Estimate risk
- Estimate operational damage
- Calculate urgency
- Detect business impact

Playbook:

```text
13-hotspot-prioritization-agent.md
```

---

## 6. Sensor Eligibility Gate

The sensor acts as a validation gate.

Responsibilities:

- Validate engineering relevance
- Reject hallucinations
- Reject low-confidence findings
- Validate implementation feasibility
- Validate operational impact

Playbook:

```text
14-sensor-eligibility-gate-agent.md
```

---

## 7. Backlog Generator

Transforms validated findings into actionable engineering work.

Responsibilities:

- Create issues
- Generate implementation plans
- Define acceptance criteria
- Generate technical specifications
- Generate rollout strategies

Playbook:

```text
15-backlog-feature-generator-agent.md
```

---

## 8. Implementation Agent

Executes engineering work.

Responsibilities:

- Implement fixes
- Create code changes
- Create pull requests
- Update tests
- Update documentation

Playbook:

```text
16-implementation-agent.md
```

---

## 9. Review Agent

Validates implementation quality.

Responsibilities:

- Review PRs
- Validate architecture consistency
- Validate engineering standards
- Validate reliability
- Validate observability
- Validate performance regressions

Playbook:

```text
17-code-review-agent.md
```

---

# Scheduler Execution Order

| Stage | Scheduler | Playbook |
|---|---|---|
| 0 | Scheduler 00 | 18-scheduler-orchestrator-agent.md |
| 1 | Scheduler 01.1 | 01-bug-panic-discovery-agent.md |
| 1 | Scheduler 01.2 | 02-reliability-resilience-discovery-agent.md |
| 1 | Scheduler 01.3 | 03-observability-discovery-agent.md |
| 1 | Scheduler 01.4 | 04-performance-discovery-agent.md |
| 1 | Scheduler 01.5 | 05-testing-gap-discovery-agent.md |
| 1 | Scheduler 01.6 | 06-security-discovery-agent.md |
| 1 | Scheduler 01.7 | 07-maintainability-architecture-discovery-agent.md |
| 1 | Scheduler 01.8 | 08-cloud-sdk-infra-discovery-agent.md |
| 1 | Scheduler 01.9 | 09-documentation-operational-readiness-discovery-agent.md |
| 2 | Scheduler 02 | 11-dedup-correlation-agent.md |
| 3 | Scheduler 03 | 12-root-cause-analysis-agent.md |
| 4 | Scheduler 04 | 13-hotspot-prioritization-agent.md |
| 5 | Scheduler 05 | 14-sensor-eligibility-gate-agent.md |
| 6 | Scheduler 06 | 15-backlog-feature-generator-agent.md |
| 7 | Scheduler 07 | 16-implementation-agent.md |
| 8 | Scheduler 08 | 17-code-review-agent.md |
| 9 | Scheduler 09 | CI/CD validation |

---

# Parallel Execution Model

Discovery radars are designed for parallel execution.

Example:

```text
Scheduler 01.1 → Bugs
Scheduler 01.2 → Reliability
Scheduler 01.3 → Observability
Scheduler 01.4 → Performance
```

This dramatically increases discovery throughput.

---

# Artifact Driven Architecture

The harness is artifact-driven.

Each stage produces artifacts consumed by the next stage.

Example:

```text
Discovery → findings.json
Correlation → correlated-findings.json
Prioritization → prioritized-hotspots.json
Backlog → issues.json
Implementation → PR metadata
Review → review-report.json
```

This guarantees:

- Deterministic state
- Replayability
- Auditability
- Traceability

---

# Recommended Repository Structure

```text
/playbook
    /discovery
    /correlation
    /analysis
    /sensor
    /backlog
    /implementation
    /review
    /orchestration

/artifacts
    /discovery
    /correlation
    /prioritized
    /backlog
    /implementation
    /review

/knowledge
    /architecture
    /patterns
    /reliability
    /security
    /performance
```

---

# Best Practices

## 1. Keep agents specialized

Never create a generic mega-agent.

Small specialized agents are more reliable.

## 2. Keep sessions stateless

Sessions should never depend on runtime memory.

Always depend on artifacts.

## 3. Use deterministic outputs

Prefer:

- JSON
- Structured markdown
- Schemas
- Contracts

Avoid free-form outputs.

## 4. Avoid implementation during discovery

Discovery agents should never modify code.

## 5. Validate before backlog generation

The sensor gate is critical to prevent noise amplification.

## 6. Parallelize discovery aggressively

Discovery is the most scalable stage.

---

# Long Term Vision

This harness architecture evolves toward:

- Autonomous engineering systems
- Continuous architecture governance
- AI-driven reliability engineering
- Self-healing platforms
- Autonomous technical debt reduction
- Continuous operational hardening

---

# Final Philosophy

The objective is not replacing engineers.

The objective is:

> Increase engineering leverage.

The harness continuously scans, validates and improves software systems while engineers focus on higher-level decisions, architecture and innovation.
