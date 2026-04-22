---
WorkflowId: WebOS_Engine_PHP_Update-Reaudit
WorkflowName: WebOS Engine PHP Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebOS_Engine_PHP_Update
Domain: WebOS
TargetKind: "Engine.Base"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/PHP/"
    FileName: "{EngineName}.update.reaudit.md"
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
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "/Aliens/manifest.json"
---

# WebOS_Engine_PHP_Update-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebOS_Engine_PHP_Update`. Read-only on Code.

## [01] Targets
- File MUST exist: `/Aliens/WebOS/{LatestVersion}/Engine/{EngineName}.php`

## [02] Inputs (Update Mode — Strict)
ReauditMode = `update`. ALL four inputs required (incl. pre-Code Audit at `.../PHP/{EngineName}.update.audit.md`).

## [03] Verification Catalog (PHP Engine Update Baseline)
### 3.1 Backward Compatibility (Update-Critical)
- Public API contract MUST NOT regress unless RI authorizes (with migration notes).
- Existing public method removal/rename without RI => `violation`.
- Existing manifest entry removal without RI => `violation`.
- Line-loss/shrink without RI authorization => `violation`.

### 3.2 File Skeleton Preservation
- `<?php` start preserved; no accidental `?>` close.
- AliensStyle CodeHeader preserved + `LastUpdated` bumped + Author = `AlienCyborg`.
- CodeFooter preserved.

### 3.3 Engine Discipline
- `$echo`+`d()` discipline preserved/added.
- Error envelope shape preserved (success + error paths same shape).
- Limits/timeouts/batching for hot paths preserved.

### 3.4 Out-of-Scope Sensitivity
- Update-Reaudit is STRICTER on out-of-scope detection: any new public function, new manifest entry, new include, new side-effect not traceable to an RI is severity `violation`.

### 3.5 Multi-batch policy (Large Files)
- For files >= 1000 lines: full rewrite forbidden; multi-batch small patches only.
- If diff suggests full rewrite without RI authorization => `violation`.

## [04]+[05]+[06]+[07]
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
