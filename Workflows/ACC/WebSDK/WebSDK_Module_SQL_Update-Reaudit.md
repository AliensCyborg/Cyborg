---
WorkflowId: WebSDK_Module_SQL_Update-Reaudit
WorkflowName: WebSDK Module SQL Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_SQL_Update
Domain: WebSDK
TargetKind: "Module/SQL"
ReauditMode: update
Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-18
Author: AlienCyborg
Outputs:
  RunRecord:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileNames: ["Reaudit.md", "Reaudit.json"]
  DeterministicArtifact:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/SQL/"
    FileName: "{TableName}.Update.reaudit.md"
Safety:
  NoDelete: true
  ReadOnlyOnCode: true
  ProdWrite: "no"
  DbTouch: "not-required"
  MissingTargetPolicy: "error"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Reaudit.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
---

# WebSDK_Module_SQL_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebSDK_Module_SQL_Update`. Read-only on Code.

## [01] Targets
- Format: `ModuleName/TableName`
- File MUST exist: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/sql/{TableName}.sql`

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. ALL four inputs required (incl. pre-Code Audit at `.../SQL/{TableName}.Update.audit.md`).

## [03] Verification Catalog (SQL Update Baseline)
### 3.1 Schema Backward Compatibility (Update-Critical)
- Existing column drop/rename/type-narrow => MUST be an RI with `migration:` note; otherwise `violation`.
- Existing index drop without RI => `violation`.
- Foreign-key direction change without RI => `violation`.

### 3.2 Naming Hygiene
- Singular Entity Name Rule preserved.
- Database prefix law preserved (no concrete prefix hardcoded).

### 3.3 File Skeleton Preservation
- AliensStyle SQL CodeHeader preserved + `LastUpdated` bumped + Author = `AlienCyborg`.
- CodeFooter preserved.

### 3.4 Safety
- No DROP / TRUNCATE without explicit RI + approval guard.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
