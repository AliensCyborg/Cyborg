---
WorkflowId: WebSDK_Module_Screen-Reaudit
WorkflowName: WebSDK Module Screen (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_Screen
Domain: WebSDK
TargetKind: "Module/Screen"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Screen/"
    FileName: "{ScreenName}.reaudit.md"
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
  - "Workflows/ACC/_Refs/ACC.UI.Premium.md"
  - "/Aliens/manifest.json"
---

# WebSDK_Module_Screen-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebSDK_Module_Screen`. Read-only on Code.

## [01] Targets
- Format: `ModuleName/ScreenName`
- File: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/screen/{ScreenName}.php`

## [02] Inputs
ReauditMode = `create`.

## [03] Verification Catalog (Screen Baseline)
### 3.1 File Skeleton
- AliensStyle CodeHeader; Author = `AlienCyborg`; CodeFooter present.
- `<?php` start; `Access();` near top.

### 3.2 Screen Surface
- WebApp naming prefix rule respected when wired into a WebApp (basename starts with `AppName.` when applicable).
- Section blocks delimited with banner comments.
- All user-visible strings ready for i18n (no hardcoded English-only strings unless RI authorizes).
- No business logic; presentation layer only (delegate to module class).

### 3.3 UI Premium Cross-Check
- Refer `_Refs/ACC.UI.Premium.md` baseline for layout/spacing/contrast hints.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
