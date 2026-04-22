---
WorkflowId: WebApp_CSS-Reaudit
WorkflowName: WebApp CSS (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_CSS
Domain: WebApp
TargetKind: "App.CSS"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/CSS/"
    FileName: "0.reaudit.md"
Safety:
  NoDelete: true
  ReadOnlyOnCode: true
  ProdWrite: "no"
  MissingTargetPolicy: "error"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Reaudit.md"
  - "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  - "/Aliens/manifest.json"
---

# WebApp_CSS-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_CSS` (Create / global design system). Read-only on Code.

## [01] Targets
- File: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/User/css/0.css`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (CSS Baseline)
### 3.1 AliensStyle Skeleton
- CSS CodeHeader (banner) + CodeFooter present.
- Author = `AlienCyborg`.

### 3.2 Design System
- Variables defined (colors / spacing / typography / radii).
- Utility classes namespaced; no global tag-only overrides without justification.
- Premium UI per `ACC.UI.Premium.md` (no missing required tokens).

### 3.3 Safety
- No `@import` of remote URLs without RI.
- No `!important` chains beyond reasonable use; flag overuse.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
