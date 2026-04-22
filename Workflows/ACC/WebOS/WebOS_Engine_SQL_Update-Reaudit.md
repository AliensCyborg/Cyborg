---
WorkflowId: WebOS_Engine_SQL_Update-Reaudit
WorkflowName: WebOS Engine SQL Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebOS_Engine_SQL_Update
Domain: WebOS
TargetKind: "Engine.Base"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/"
    FileName: "{EngineName}.update.reaudit.md"
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
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "/Aliens/manifest.json"
---

# WebOS_Engine_SQL_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebOS_Engine_SQL_Update`. Read-only on Code.

## [01] Targets
- File MUST exist: `/Aliens/WebOS/{LatestVersion}/unit/sql/{EngineName}.sql`

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. Pre-Code Audit at `.../SQL/{EngineName}.update.audit.md` MUST exist.

## [03] Verification Catalog (SQL Engine Update Baseline)
### 3.1 Schema Backward Compatibility (Update-Critical)
- Existing column drop/rename/type-narrow without RI => `violation`.
- Existing index drop without RI => `violation`.
- Foreign-key direction change without RI => `violation`.

### 3.2 Naming Hygiene + Prefix Law
- Preserved per WebSDK ref.

### 3.3 Safety
- No DROP / TRUNCATE without RI + approval.

### 3.4 Out-of-Scope Sensitivity
- Update-Reaudit is STRICTER on out-of-scope: any new table, new column unrelated to RI is severity `violation`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
