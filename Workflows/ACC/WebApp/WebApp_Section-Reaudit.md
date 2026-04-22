---
WorkflowId: WebApp_Section-Reaudit
WorkflowName: WebApp Section (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_Section
Domain: WebApp
TargetKind: "App.Section"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Section/"
    FileName: "{SectionName}.reaudit.md"
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
  - "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  - "/Aliens/manifest.json"
---

# WebApp_Section-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_Section` (Create). Read-only on Code.

## [01] Targets
- File: `.../theme/{AppName}_Basic/User/section/{SectionName}.php`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (Section Baseline)
### 3.1 AliensStyle Skeleton
- CodeHeader + CodeFooter; Author = `AlienCyborg`; section comments.

### 3.2 Naming Law (Strict)
- Filename strictly `{AppName}.{SectionName}` per naming ref. No double prefix.

### 3.3 Surface
- No business logic outside SDK contracts.
- All user inputs escaped (XSS-safe); no hardcoded secrets.
- Premium UI hooks per `ACC.UI.Premium.md`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
