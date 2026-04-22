---
WorkflowId: PySDK_Module_SQL_Update
Type: Workflow_Plural
Domain: PySDK
TargetKind: "Module"
Scope: Update existing module SQL bundle (index.sql) (UPDATE-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-31
Summary: >
  Plural UPDATE-only workflow that updates one or multiple existing PySDK module SQL bundles.
  This workflow MUST NOT create missing sql/index.sql. For each module target, it orchestrates:
  Audit (Read-only) -> Planning (Update) -> Code (Modify-only) -> Documentation (Update notes).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/PySDK/PySDK_Module_SQL_Update-Audit.md"
  - "Workflows/ACC/PySDK/PySDK_Module_SQL_Update-Planning.md"
  - "Workflows/ACC/PySDK/PySDK_Module_SQL_Update-Code.md"
  - "Workflows/ACC/PySDK/PySDK_Module_SQL_Update-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated list. Each item must be a ModuleName (NO slash)."
  - Name: Description
    Type: string
    Required: false
    Notes: "Any human language. Update requirements, constraints, and intent."
Defaults:
  PySDKVersion: "latest"
  VersionFallback: "Py.26.00.00"
  NoDelete: true
  ModifyOnly: true
  ApprovalMode: "from /Aliens/manifest.json"
PermissionsRequested:
  ProdWrite: true
  DbTouch: "no (file update only; no DB execution)"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Audit", "Planning", "Code", "Documentation"]
UpdatePolicy:
  MissingTarget: "ERROR (do not create)"
  ExistingTarget: "Modify-only (no delete, no destructive changes unless approved)"
FailureStrategy:
  FailFast: false
  OnTargetMissing: "skip Audit+Planning+Code+Doc for that target and record Failed(TargetMissing)"
  OnAuditFail: "skip Planning+Code+Doc for that target"
  OnPlanningFail: "skip Code+Doc for that target"
  OnCodeFail: "Documentation may run but must clearly mark code update failed"
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/PySDK/"
---

# PySDK_Module_SQL_Update.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`PySDK_Module_SQL_Update` ek **Plural UPDATE-only** workflow hai jo **existing** module SQL bundle ko update karwata hai.

Example:
- `Workflow("PySDK_Module_SQL_Update", "Teacher", "Existing sql/index.sql me new column add karo")`
- `Workflow("PySDK_Module_SQL_Update", "Teacher,User", "In dono modules ke index.sql ko harden karo")`

---

## Target Grammar (Strict)
Each target item must be:
- `ModuleName` (NO slash)

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Table names in `sql/index.sql` MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Table Naming Collision Gate (Non-Negotiable):
- New table names MUST be checked against existing schema to avoid semantic duplicates
  (e.g., token reorder like `w3_verified_contract` vs `w3_contract_verified`).
- Collision risk => fail fast and require explicit naming decision.

---

## UPDATE-only Contract (Non-Negotiable)
For each module target:
1) Resolve `{SqlFile}` path.
2) If `{SqlFile}` does NOT exist:
   - This is an UPDATE workflow, so:
   - MUST record `Failed(TargetMissing)`
   - MUST NOT create file/folder
   - MUST NOT call child workflows for this target
3) If `{SqlFile}` exists:
   - MUST call child workflows in order:
     1) `PySDK_Module_SQL_Update-Audit` (read-only)
     2) `PySDK_Module_SQL_Update-Planning` (update plan)
     3) `PySDK_Module_SQL_Update-Code` (modify-only update)
     4) `PySDK_Module_SQL_Update-Documentation` (final docs + update notes)

---

## Orchestration Contract (per target)
### Step 1 — Build RunContext
- Resolve developer manifest: `/Aliens/manifest.json`
- Resolve approval mode: `auto | manual`
- Resolve versions:
  - preferred: latest available
  - fallback: `Py.26.00.00`

### Step 2 — Resolve paths
Paths (per module):
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`
- `{SqlFile}    = {ModuleRoot}/sql/index.sql`

### Step 3 — Call child workflows in order
Child calls MUST pass at least:
- `Targets`: single target item (NOT full list)
- `Description`: inherited
- `ParentWorkflowId`: `PySDK_Module_SQL_Update`

Calls:
1) `Workflow("PySDK_Module_SQL_Update-Audit", "<Module>", "<Description>")`
2) `Workflow("PySDK_Module_SQL_Update-Planning", "<Module>", "<Description>")`
3) `Workflow("PySDK_Module_SQL_Update-Code", "<Module>", "<Description>")`
4) `Workflow("PySDK_Module_SQL_Update-Documentation", "<Module>", "<Description>")`

---

## What this workflow MUST NOT do
- Is workflow me direct SQL file content generate/update rules detail me nahi likhne.
- Is workflow me direct filesystem/db actions implement nahi karne.
- Sirf orchestration + validation + run-summary.

---

## Success Criteria
- For existing targets: Audit->Planning->Code->Documentation ran in order
- For missing targets: `Failed(TargetMissing)` recorded with zero creation
- `sql/index.sql` updated per plan (modify-only)
- Docs updated under `/Aliens/Docs/PySDK/Module/{Module}/SQL/index.md`
- Run summary includes created/updated files list
