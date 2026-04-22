---
WorkflowId: WebSDK_Module_SQL-Reaudit
WorkflowName: WebSDK Module SQL (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_SQL
Domain: WebSDK
TargetKind: "Module/SQL"
ReauditMode: create
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
    FileName: "{TableName}.reaudit.md"
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

# WebSDK_Module_SQL-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebSDK_Module_SQL`. Read-only on Code.

## [01] Targets
- Format: `ModuleName` OR `ModuleName/TableName`
- File: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/sql/{TableName}.sql`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (SQL Baseline)
### 3.1 File Skeleton
- AliensStyle SQL CodeHeader (banner + Author = `AlienCyborg`); CodeFooter present.

### 3.2 Schema Hygiene
- Singular Entity Name Rule: TableName MUST be singular.
- Database prefix law: NO `Coinpods_...` (or any concrete prefix) hardcoded; base names only.
- Primary key + uid column conventions per WebSDK ref.
- Indexes documented with rationale (no random indexes).
- Foreign key references match canonical module table names.
- Column types appropriate (no `TEXT` where `VARCHAR(N)` fits, etc.).
- `created` / `modified` / `status` / `uid` standard columns present where applicable.

### 3.3 Safety
- No destructive statements (DROP / TRUNCATE) unless RI authorizes + approval guard documented.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
