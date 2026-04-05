---
WorkflowId: WebApp
WorkflowName: WebApp (Orchestration)
Type: Workflow_Plural
Domain: WebApp
Scope: Orchestrate complete enterprise-grade WebApp build via child workflows
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    FileName: "WebApp.Run.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
  - "Orchestration only"
Requires:
  - Workflows/ACC/_README.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("WebApp", "{AppName}", "{Description}")`

This is an orchestration workflow for building a complete enterprise-grade WebApp.

Hard rule (non-negotiable):
- **Quality > Speed** (10 hours, 10 days, months—allowed)
- Safe-by-default: **NoDelete = true**
- Any artifact/report must be stored under `.Alien/{Developer}/...`

---

# [1] Universal Includes (Mandatory Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Targets.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Targets (AppName) Rules
- Targets format: `AppName` (NameOnly)
- Comma allowed: `App1, App2` => repeat-run orchestration for each app.
- Slash `/` is NOT expected here.

---

# [3] Required Child Workflows (Strict Order)
For each `{AppName}` run these mandatory child workflows:

1) `Workflow("WebApp-Research", "{AppName}", "{Description}")`
2) `Workflow("WebApp-Planning", "{AppName}", "{Description}")`
3) `Workflow("WebApp-Planner", "{AppName}", "{Description}")`
4) `Workflow("WebApp-Structure", "{AppName}", "{Description}")`
5) `Workflow("WebApp-Development", "{AppName}", "{Description}")`
6) `Workflow("WebApp-Audit", "{AppName}", "{Description}")`
7) `Workflow("WebApp-Refactor", "{AppName}", "{Description}")`

Notes:
- Each child workflow MUST be runnable standalone.
- Each child workflow MUST write deterministic artifacts under `.Alien/{Developer}/...`.

---

# [4] Storage Policy (Mandatory)
All outputs must be stored in developer workspace:
- Research: `/Aliens/.Alien/{Developer}/Research/WebApp/{AppName}/...`
- Planning: `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/...`
- Tasks CSV: `/Aliens/Project/{Developer_Username}/{AppName}_Planner.csv`
- Audit: `/Aliens/.Alien/{Developer}/Audit/WebApp/{AppName}/...`
- Runs/logs: `/Aliens/.Alien/{Developer}/Runs/...`

---

# [5] Orchestration Rules (Enterprise)
- Do NOT do deep code-generation inside `WebApp` itself.
- `WebApp` is orchestration-only and must delegate to child workflows.
- If any child workflow fails, orchestration MUST stop and return:
  - failed step id
  - error code
  - artifact paths created so far

---

# [6] Standard Errors
- `WF_TARGET_INVALID`
- `WF_DEPENDENCY_MISSING`
- `WF_RESEARCH_REQUIRED`
- `WF_PLANNING_REQUIRED`
- `WF_PLANNER_REQUIRED`
- `WF_STRUCTURE_REQUIRED`
- `WF_DEVELOPMENT_INCOMPLETE`
- `WF_AUDIT_REQUIRED`
