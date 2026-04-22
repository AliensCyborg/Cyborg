---
WorkflowId: WebApp_CSS_Update-Reaudit
WorkflowName: WebApp CSS Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_CSS_Update
Domain: WebApp
TargetKind: "App.CSS"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/CSS/"
    FileName: "0.update.reaudit.md"
Safety:
  NoDelete: true
  ReadOnlyOnCode: true
  ProdWrite: "no"
  MissingTargetPolicy: "error"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Reaudit.md"
  - "/Aliens/manifest.json"
---

# WebApp_CSS_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_CSS_Update`. Read-only on Code.

## [01] Targets
- File MUST exist: `.../User/css/0.css`

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. Pre-Code Audit at `.../CSS/0.update.audit.md` MUST exist.

## [03] Verification Catalog (CSS Update Baseline)
### 3.1 Backward Compatibility (Update-Critical)
- Existing CSS variables / class names MUST NOT be removed/renamed without RI.
- Existing utility classes preserved.
- No silent breaking selector changes.

### 3.2 AliensStyle Preservation
- CodeHeader/CodeFooter preserved; `LastUpdated` bumped; Author = `AlienCyborg`.

### 3.3 Out-of-Scope Sensitivity
- Update-Reaudit STRICTER: any new variable/class/utility unrelated to RI is severity `violation`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
