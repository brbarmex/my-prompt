┌──────────────────────────────────────────────────────────────┐
│                 BRBARMEX AI HARNESS                          │
│        AI-Assisted Enterprise SDLC Pipeline                  │
└──────────────────────────────────────────────────────────────┘


                         TARGET REPOSITORY
                    (Application Repository)
                                  │
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 1                                                 │
│ DISCOVERY / HOTSPOT SCAN AGENT                              │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Wide & shallow repository scan                            │
│ • Detect hotspots                                           │
│ • Detect technical debt                                     │
│ • Detect reliability/security/performance risks             │
│ • Detect observability/test gaps                            │
│ • NO backlog generation                                     │
│ • NO code changes                                           │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/discovery/<timestamp>-hotspot-scan
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/hotspot-map.json
     runs/<timestamp>/hotspot-map.md



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 2                                                 │
│ DEEP ANALYSIS AGENT                                         │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Analyze ONE hotspot deeply                                │
│ • Generate evidence-based findings                          │
│ • Reliability analysis                                      │
│ • Security analysis                                         │
│ • Performance analysis                                      │
│ • Observability analysis                                    │
│ • Testing analysis                                          │
│ • NO backlog generation                                     │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/deep-analysis/<timestamp>-<hotspot-id>
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/findings/<hotspot-id>.json
     runs/<timestamp>/findings/<hotspot-id>.md



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 3                                                 │
│ DEDUPLICATION & CORRELATION AGENT                           │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Remove duplicated findings                                │
│ • Group correlated findings                                 │
│ • Identify root causes                                      │
│ • Prevent backlog inflation                                 │
│ • Consolidate opportunities                                 │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/correlation/<timestamp>-dedup-correlation
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/candidate-features.json
     runs/<timestamp>/candidate-features.md



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 4                                                 │
│ SENSOR / ELIGIBILITY GATE AGENT                             │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Evaluate engineering ROI                                  │
│ • Evaluate implementation readiness                         │
│ • Evaluate operational impact                               │
│ • Evaluate reliability impact                               │
│ • Score candidates (0-100)                                  │
│ • Reject weak/vague/speculative backlog                     │
│ • Approve only score >= 90                                  │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/sensor/<timestamp>-eligibility-gate
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/sensor-results.json
     runs/<timestamp>/sensor-results.md



                                  │
                    Score >= 90 ? │
                         ┌────────┴────────┐
                         │                 │
                         ▼                 ▼
                  REJECTED           APPROVED
                         │                 │
                         │                 ▼
                         │
                         │
                         ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 5                                                 │
│ BACKLOG FEATURE GENERATOR AGENT                             │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Generate enterprise backlog                               │
│ • Generate RFC-style specification                          │
│ • Generate AI-ready backlog                                 │
│ • Generate Cockburn use cases                               │
│ • Generate DoR / DoD                                        │
│ • Generate observability/testing requirements               │
│ • Optional issue creation                                   │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/backlog/<timestamp>-backlog-generation
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/backlog-features/*.md
     runs/<timestamp>/backlog-features/*.json
                                  │
                                  ▼
                        Issue Tracker
                    (Jira / GitHub / Linear)



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 6                                                 │
│ IMPLEMENTATION DISPATCH AGENT                               │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Select approved backlog                                   │
│ • Ensure issue eligibility                                  │
│ • Prevent duplicated implementations                        │
│ • Dispatch implementation jobs                              │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/implementation-dispatch/<timestamp>-dispatch
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/implementation-dispatch.json



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ IMPLEMENTATION AGENT                                        │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Clone application repo                                    │
│ • Update origin/develop                                     │
│ • Create feature branch                                     │
│ • Implement backlog                                         │
│ • Add/update tests                                          │
│ • Run unit tests                                            │
│ • Run system/e2e tests                                      │
│ • Update docs                                               │
│ • Open PR                                                   │
│ • Wait for CI/CD                                            │
│ • Fix pipeline failures                                     │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
                    APPLICATION REPOSITORY
                                  │
                                  ▼
                         Pull Request Opened



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ Scheduler 7                                                 │
│ REVIEW DISPATCH AGENT                                       │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Detect PRs created by AI                                  │
│ • Prepare review execution                                  │
│ • Correlate PR with backlog                                 │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
               brbarmex-ai-harness-artefact
                                  │
                                  ▼
     Branch:
     harness/review-dispatch/<timestamp>-review-dispatch
                                  │
                                  ▼
     Artifacts:
     runs/<timestamp>/review-dispatch.json



                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────┐
│ CODE REVIEW AGENT                                           │
│--------------------------------------------------------------│
│ Responsibilities:                                           │
│ • Review ONLY changed code                                  │
│ • Validate backlog compliance                               │
│ • Validate reliability                                      │
│ • Validate observability                                    │
│ • Validate testing                                           │
│ • Validate production safety                                │
│ • Add inline comments                                       │
│ • Avoid blocking due legacy code                            │
│ • Suggest follow-up backlog for unrelated findings          │
└──────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
                           Pull Request
                                  │
                                  ▼
                         CI/CD PIPELINE
                                  │
                     ┌────────────┴────────────┐
                     │                         │
                     ▼                         ▼
                 FAILED                    PASSED
                     │                         │
                     ▼                         ▼
          Back to Implementation         READY TO MERGE
                  Agent                         │
                                                ▼
                                         Merge into develop



┌──────────────────────────────────────────────────────────────┐
│                    KEY DESIGN PRINCIPLES                    │
├──────────────────────────────────────────────────────────────┤
│ • Small context windows                                     │
│ • Specialized agents                                        │
│ • Artifact-driven orchestration                             │
│ • Versioned evidence                                        │
│ • Deterministic workflows                                   │
│ • Deduplication before backlog                              │
│ • Sensor before issue creation                              │
│ • PR governance                                             │
│ • Production safety first                                   │
│ • Operational observability                                 │
│ • AI-agent friendly architecture                            │
└──────────────────────────────────────────────────────────────┘
