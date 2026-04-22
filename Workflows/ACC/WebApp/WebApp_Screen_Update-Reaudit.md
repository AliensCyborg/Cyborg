---
WorkflowId: WebApp_Screen_Update-Reaudit
WorkflowName: WebApp Screen Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_Screen_Update
Domain: WebApp
TargetKind: "App.Screen"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Screen/"
    FileName: "{ScreenName}.update.reaudit.md"
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

# WebApp_Screen_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_Screen_Update`. Read-only on Code.

## [01] Targets
- File MUST exist: `.../User/screen/{ScreenName}.php`

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. Pre-Code Audit at `.../Screen/{ScreenName}.update.audit.md` MUST exist.

## [03] Verification Catalog (Screen Update Baseline)
### 3.1 Backward Compatibility (Update-Critical)
- Existing section/component composition preserved unless RI authorizes.
- Existing public hooks (id/data-* / route binding) preserved.

### 3.2 AliensStyle Preservation
- CodeHeader + CodeFooter preserved; `LastUpdated` bumped; Author = `AlienCyborg`.

### 3.3 Safety
- No new secrets / hardcoded credentials.
- All new user inputs escaped (XSS-safe).
- No new external HTTP calls without RI.

### 3.4 Out-of-Scope Sensitivity
- Update-Reaudit STRICTER: any new section/feature unrelated to RI is severity `violation`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
