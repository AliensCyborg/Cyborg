---
WorkflowId: WebSDK_Module_SQL-Code
WorkflowName: WebSDK Module SQL Code Generator
Type: Workflow_Singular-Code
ParentWorkflowId: WebSDK_Module_SQL
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "ModuleName, ModuleName2, ..."
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md"
Produces:
  - SqlFile: "/Aliens/WebSDK/{PHP.Version}/Module/{Module}/sql/index.sql"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  Refs:
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

# WebSDK_Module_SQL-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## Role
PlanningDoc ko SSOT maan kar `sql/index.sql` create/update karo.

---

## [01] Hard dependency
PlanningDoc MUST exist:
- `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md`

If missing => fail with `Failed(PlanningMissing)`.

---

## [02] Path Resolution
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
- `{SqlFolder} = {ModuleRoot}/sql/`
- `{SqlFile} = {SqlFolder}/index.sql`

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- SQL table names written to `index.sql` MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Table Naming Collision Gate (Non-Negotiable):
- If PlanningDoc indicates a new table name that is a semantic duplicate of an existing table
  (e.g., token reorder like `w3_verified_contract` vs `w3_contract_verified`),
  then STOP and fail with `ACC_ERR_TABLE_NAME_COLLISION`.

Folder create rule:
- If `{SqlFolder}` missing, create it (non-destructive).
- Do NOT create missing `{ModuleRoot}` (dependency error).

---

## [03] Create vs Update
- Create: generate a complete schema bundle according to plan.
- Update: apply minimal diffs, preserve backward compatibility.

No destructive operations unless:
- PlanningDoc explicitly marks as approved
- And approval policy allows it

---

## [04] Output RunRecordItem (mandatory)
Write:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`
Include:
- SqlFile path
- PlanMode
- created/updated flag
- high-level table summary
