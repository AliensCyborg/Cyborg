---
WorkflowId: WebApp_Component-Reaudit
WorkflowName: WebApp Component (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_Component
Domain: WebApp
TargetKind: "App.Component"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Component/"
    FileName: "{ComponentName}.reaudit.md"
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
  - "Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md"
  - "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  - "/Aliens/manifest.json"
---

# WebApp_Component-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_Component` (Create). Read-only on Code.

## [01] Targets
- File: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/User/component/{ComponentName}.php`
- Optional CSS: `.../User/css/{ComponentName}.css`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (WebApp Component Baseline)
### 3.1 AliensStyle Skeleton
- PHP CodeHeader (banner) + CodeFooter present.
- Author = `AlienCyborg`; `LastUpdated` set.
- Section comments + per-function code-headers.

### 3.2 Naming Law (Strict)
- Filename strictly matches `{AppName}.{ComponentName}` prefix law per `WebApp_Naming_and_Screens.md`.
- No double prefix.

### 3.3 Surface
- No global state writes; idempotent render.
- No echo of secrets / no hardcoded credentials.
- All user inputs escaped (XSS-safe).

### 3.4 UI Premium
- Premium UI guidance per `ACC.UI.Premium.md` (no missing required hooks).

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
