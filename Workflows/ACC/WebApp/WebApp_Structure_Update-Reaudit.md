---
WorkflowId: WebApp_Structure_Update-Reaudit
WorkflowName: WebApp Structure Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebApp_Structure_Update
Domain: WebApp
TargetKind: "App.Structure"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Structure/"
    FileName: "Structure.update.reaudit.md"
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

# WebApp_Structure_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebApp_Structure_Update`. Read-only on Code.

## [01] Targets
- App scaffold path MUST exist: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. Pre-Code Audit at `.../Structure/Structure.update.audit.md` MUST exist.

## [03] Verification Catalog (Structure Update Baseline)
### 3.1 Backward Compatibility (Update-Critical)
- No deletes (NoDelete law).
- Existing folder layout preserved.
- Existing files MUST NOT be removed/renamed without RI.

### 3.2 Naming Law
- New folders/files follow `WebApp_Naming_and_Screens.md` strictly.

### 3.3 Out-of-Scope Sensitivity
- Update-Reaudit STRICTER: any new file/folder unrelated to RI is severity `violation`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
