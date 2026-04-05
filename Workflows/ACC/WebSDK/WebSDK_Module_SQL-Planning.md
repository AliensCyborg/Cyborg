---
WorkflowId: WebSDK_Module_SQL-Planning
WorkflowName: WebSDK Module SQL Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: WebSDK_Module_SQL
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "ModuleName, ModuleName2, ..."
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/WebSDK.md"
NonNegotiables:
  - NoDelete: true
  - DefaultVersionFallback: "PHP.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.sql"
---

# WebSDK_Module_SQL-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Role
Yeh workflow per module SQL ke liye planning banata hai:
- version resolve
- module root verify
- current `sql/index.sql` presence check
- Create vs Update mode decide
- schema requirements extract from Description

---

## [01] Target Interpretation
Targets:
- `ModuleName` only

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- SQL table names produced must be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

If target contains `/`:
- fail with `ACC_ERR_TARGET_INVALID`

---

## [02] Path Resolution (SSOT)
Resolve version:
- primary: latest available
- fallback: `PHP.26.00.00`

Paths:
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
- `{SqlFile} = {ModuleRoot}/sql/index.sql`

---

## [03] Dependency Gate
Required:
- `{ModuleRoot}` must exist

If missing:
- Mark `Failed(DependencyMissing)`
- ErrorCode: `WF_MODULE_NOT_FOUND`
- Action: run `WebSDK_Module` first

---

## [04] PlanMode (Create vs Update)
- If `{SqlFile}` exists => PlanMode: `Update`
- Else => PlanMode: `Create`

---

## [05] Planner MUST record (minimum)
Planner doc must include:
- ModuleName
- PHP.Version used
- PlanMode
- Target files:
  - sql/index.sql
- Schema decisions:
  - table list (and naming)
  - primary keys
  - indexes
  - status/soft-delete rule
  - required default columns policy (id, uid, created, modified, status, app, user) if applicable
- Compatibility notes:
  - no destructive migrations unless explicitly approved

### [05.1] Table Naming Collision Gate (Non-Negotiable)
- Planner MUST compare any new table names with existing schema (from current `index.sql` and class references).
- If a semantic duplicate/alias is detected (e.g., token reorder like `w3_verified_contract` vs `w3_contract_verified`):
  - FAIL fast and require explicit naming decision in Description.

---

## [06] Save Outputs
- PlanningDoc: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md`
- RunRecordItem: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md`
