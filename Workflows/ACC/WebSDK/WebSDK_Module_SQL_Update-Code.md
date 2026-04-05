---
WorkflowId: WebSDK_Module_SQL_Update-Code
WorkflowName: WebSDK Module SQL Update Code
Type: Workflow_Singular-Code
ParentWorkflowId: WebSDK_Module_SQL_Update
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-31
Inputs:
  - Targets: "ModuleName"
  - Description: "Any human language requirement"
Consumes:
  - AuditArtifact: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.update.audit.md"
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
  - ModifyOnly: true
  - DefaultVersionFallback: "PHP.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.sql"
---

# WebSDK_Module_SQL_Update-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## Role
PlanningDoc ko SSOT maan kar **existing** `sql/index.sql` ko update karo (update-only + modify-only).

---

## [01] Hard dependencies (Update-only)
MUST exist:
- AuditArtifact: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.update.audit.md`
- PlanningDoc: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md`
- SqlFile: `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/sql/index.sql`

If missing:
- AuditArtifact missing => `Failed(AuditMissing)`
- PlanningDoc missing => `Failed(PlanningMissing)`
- SqlFile missing => `Failed(TargetMissing)` (do not create)

---

## [02] Path Resolution
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
- `{SqlFolder}  = {ModuleRoot}/sql/`
- `{SqlFile}    = {SqlFolder}/index.sql`

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- SQL table names written to `index.sql` MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Table Naming Collision Gate (Non-Negotiable):
- If PlanningDoc indicates a new table name that is a semantic duplicate of an existing table
  (e.g., token reorder like `w3_verified_contract` vs `w3_contract_verified`),
  then STOP and fail with `ACC_ERR_TABLE_NAME_COLLISION`.

---

## [03] Update policy (Modify-only)
- Apply minimal diffs per plan.
- Preserve backward compatibility.
- Do not delete tables/columns/indexes unless:
  - PlanningDoc explicitly marks as approved
  - and approval policy allows it

---

## [04] Output RunRecordItem (mandatory)
Write:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`
Include:
- SqlFile path
- PlanMode: Update
- updated flag
- high-level table summary
- risk notes (if any)
