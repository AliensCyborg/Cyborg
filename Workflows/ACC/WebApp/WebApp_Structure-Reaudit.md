---
WorkflowId: WebApp_Structure-Reaudit
WorkflowName: WebApp Structure (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_Structure
Domain: WebApp
TargetKind: "App.Structure"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Structure/"
    FileName: "Structure.reaudit.md"
Safety:
  NoDelete: true
  ReadOnlyOnCode: true
  ProdWrite: "no"
  MissingTargetPolicy: "error"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Reaudit.md"
  - "Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md"
  - "/Aliens/manifest.json"
---

# WebApp_Structure-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_Structure` (Create / scaffold). Read-only on Code.

## [01] Targets
- App scaffold path: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (Structure Baseline)
### 3.1 Folder Layout (Mandatory)
- `User/screen/`, `User/section/`, `User/component/`, `User/css/`, `User/layout/` exist.
- Naming follows `WebApp_Naming_and_Screens.md` (no double prefix; `{AppName}.X` law).

### 3.2 No Business Logic in Scaffold
- Created stubs (if any) carry only AliensStyle skeleton + section comments.
- No DB writes from scaffold.

### 3.3 Safety
- No deletes performed.
- No file outside `theme/{AppName}_Basic/` touched.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
