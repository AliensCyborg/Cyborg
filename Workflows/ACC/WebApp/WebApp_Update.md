---
WorkflowId: WebApp_Update
WorkflowName: WebApp Update (Orchestration)
Type: Workflow_Plural
Domain: WebApp
Scope: Orchestrate enterprise-grade WebApp updates (update-only; no rebuild)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Update"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
    FileName: "WebApp_Update.Run.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
  - "Update-only; no rebuild from scratch"
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
`Workflow("WebApp_Update", "{AppName}", "{Description}")`

This is an orchestration workflow for updating an existing enterprise-grade WebApp.

Hard rule (non-negotiable):
- **Quality > Speed** (10 hours, 10 days, months—allowed)
- Safe-by-default: **NoDelete = true**
- Update-only: **do not rebuild from scratch**
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

1) `Workflow("WebApp_Update-Research", "{AppName}", "{Description}")`
2) `Workflow("WebApp_Update-Planning", "{AppName}", "{Description}")`
3) `Workflow("WebApp_Update-Planner", "{AppName}", "{Description}")`
4) `Workflow("WebApp_Update-Structure", "{AppName}", "{Description}")`
5) `Workflow("WebApp_Update-Development", "{AppName}", "{Description}")`
6) `Workflow("WebApp_Update-Audit", "{AppName}", "{Description}")`
7) `Workflow("WebApp_Update-Refactor", "{AppName}", "{Description}")`

Notes:
- Each child workflow MUST be runnable standalone.
- Each child workflow MUST write deterministic artifacts under `.Alien/{Developer}/...`.

---

# [4] Storage Policy (Mandatory)
All outputs must be stored in developer workspace:
- Update Research: `/Aliens/.Alien/{Developer}/Research/WebApp/{AppName}/WebApp_Update-Research.md`
- Update Planning: `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/WebApp_Update-Planning.md`
- Update Tasks CSV: `/Aliens/Project/{Developer_Username}/{AppName}_Update_Planner.csv`
- Update Audit: `/Aliens/.Alien/{Developer}/Audit/WebApp/{AppName}/WebApp_Update-Audit.md`
- Runs/logs: `/Aliens/.Alien/{Developer}/Runs/WebApp/{AppName}/...`

---

# [5] Orchestration Rules (Enterprise)
- Do NOT do deep code-generation inside `WebApp_Update` itself.
- `WebApp_Update` is orchestration-only and must delegate to child workflows.
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
