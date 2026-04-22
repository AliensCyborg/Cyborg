---
WorkflowId: WebOS_Engine_SQL-Reaudit
WorkflowName: WebOS Engine SQL (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebOS_Engine_SQL
Domain: WebOS
TargetKind: "Engine.Base"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/"
    FileName: "{EngineName}.reaudit.md"
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

# WebOS_Engine_SQL-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebOS_Engine_SQL` (Create). Read-only on Code.

## [01] Targets
- File: `/Aliens/WebOS/{LatestVersion}/unit/sql/{EngineName}.sql`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (SQL Engine Baseline)
### 3.1 File Skeleton
- AliensStyle SQL CodeHeader (banner + Author = `AlienCyborg`); CodeFooter present.

### 3.2 Schema Hygiene
- Singular Entity Name Rule respected.
- Database prefix law: NO concrete prefix hardcoded; base names only.
- Standard columns (`uid`, `created`, `modified`, `status`) where applicable.
- Indexes documented with rationale.
- Foreign-key references match canonical names.

### 3.3 Safety
- No DROP / TRUNCATE without RI authorization + approval guard.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
