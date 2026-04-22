---
WorkflowId: WebSDK_Module_API_Update-Reaudit
WorkflowName: WebSDK Module API Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_API_Update
Domain: WebSDK
TargetKind: "Module/API"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/API/"
    FileName: "{ApiName}.Update.reaudit.md"
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

# WebSDK_Module_API_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebSDK_Module_API_Update`. Read-only on Code.

## [01] Targets
- Format: `ModuleName/ApiName`
- File MUST exist: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/api/{ApiName}.php`
- Missing => `WF_TARGET_MISSING`.

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. ALL four inputs from common engine [01] required:
1. Description
2. Planning Required Items
3. Pre-Code Audit artifact: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/API/{ApiName}.Update.audit.md`
4. Post-Code state on disk

## [03] Verification Catalog (API Update Baseline)
### 3.1 Backward Compatibility (Update-Critical)
- Public input/output contract MUST NOT regress unless an RI explicitly authorizes a breaking change with migration notes.
- HTTP status codes / response envelope keys preserved.
- Removed/renamed fields => MUST appear as RI with `migration:` note.

### 3.2 File Skeleton Preservation
- `<?php` start preserved; no accidental `?>` close added.
- `Access();` still near top.
- AliensStyle CodeHeader preserved + `LastUpdated` bumped + Author = `AlienCyborg`.
- CodeFooter preserved.

### 3.3 SDK Integration
- DB table base names (no concrete prefix) — same rule as create.
- Helper-call result verification preserved/added per RI.
- `$echo`+`d()` discipline preserved/added.

### 3.4 Out-of-Scope Sensitivity
- Update-Reaudit is STRICTER on out-of-scope detection: any new public function, new manifest entry, new endpoint side-effect not traceable to an RI is severity `violation`.

### 3.5 Diff Sanity
- Line-loss / shrink without RI authorization => `violation`.
- Existing public method removal/rename without RI => `violation`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
