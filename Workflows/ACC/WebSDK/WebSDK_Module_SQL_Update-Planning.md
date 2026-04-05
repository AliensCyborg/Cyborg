---
WorkflowId: WebSDK_Module_SQL_Update-Planning
WorkflowName: WebSDK Module SQL Update Planning
Type: Workflow_Singular-Planning
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
  - ModifyOnly: true
  - DefaultVersionFallback: "PHP.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.sql"
---

# WebSDK_Module_SQL_Update-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Role
Yeh workflow per module SQL update ke liye planning banata hai:
- version resolve
- module root verify
- existing `sql/index.sql` presence check (update-only)
- Audit artifact verify
- schema requirements extract from Description
- minimal, backward-compatible update plan craft

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
- `{SqlFile}    = {ModuleRoot}/sql/index.sql`
- `{AuditArtifact} = /Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.update.audit.md`

---

## [03] Dependency Gate (Update-only)
Required:
- `{ModuleRoot}` must exist
- `{SqlFile}` must exist (UPDATE-only)
- `{AuditArtifact}` must exist (hard)

If `{ModuleRoot}` missing:
- Mark `Failed(DependencyMissing)`
- ErrorCode: `WF_MODULE_NOT_FOUND`
- Action: run `WebSDK_Module` first

If `{SqlFile}` missing:
- Mark `Failed(TargetMissing)`
- ErrorCode: `WF_SQL_FILE_MISSING`
- Action: if you want create, run `WebSDK_Module_SQL` (NOT Update)

If `{AuditArtifact}` missing:
- fail with `Failed(AuditMissing)`
- ErrorCode: `WF_AUDIT_MISSING`

---

## [04] PlanMode
Hard rule:
- PlanMode MUST be `Update` (never Create).

---

## [05] Planner MUST record (minimum)
Planner doc must include:
- ModuleName
- PHP.Version used
- PlanMode: `Update`
- Target files:
  - sql/index.sql
- Current schema snapshot (best-effort):
  - existing table list
- Proposed changes:
  - new/changed tables
  - new/changed columns
  - new/changed indexes
  - any constraint changes (only if approved)
- Compatibility notes:
  - no destructive migrations unless explicitly approved
  - how to deploy safely (ordered rollout notes)

### [05.1] Table Naming Collision Gate (Non-Negotiable)
- Planner MUST compare any new table names with existing schema.
- If a semantic duplicate/alias is detected (e.g., token reorder like `w3_verified_contract` vs `w3_contract_verified`):
  - FAIL fast and require explicit naming decision in Description.

---

## [06] Save Outputs
- PlanningDoc: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/SQL/index.plan.md`
- RunRecordItem: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md`
