---
WorkflowId: WebSDK_Module_API-Reaudit
WorkflowName: WebSDK Module API (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_API
Domain: WebSDK
TargetKind: "Module/API"
ReauditMode: create
Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-18
Author: AlienCyborg
Inputs:
  - Targets       # ModuleName/ApiName
  - Description
Outputs:
  RunRecord:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileNames:
      - "Reaudit.md"
      - "Reaudit.json"
  DeterministicArtifact:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/API/"
    FileName: "{ApiName}.reaudit.md"
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
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
---

# WebSDK_Module_API-Reaudit

## [00A] Universal Includes
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Reaudit.md`

## [00] Purpose
Post-Code verification step for `WebSDK_Module_API`. Read-only on Code.
Verifies the freshly generated/updated API file against:
- Planning's `## Required Items` block (primary source)
- The API File Skeleton baseline (secondary)
- The `## Out-of-Scope` block (forbid scope creep)

## [01] Targets
- Format: `ModuleName/ApiName` (same grammar as parent)
- Resolves to: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/api/{ApiName}.php`
- Missing target file => `WF_TARGET_MISSING`.

## [02] Required Inputs
Per common engine [01]. Mode = `create` (pre-Code Audit optional).
- Description (may be empty in baseline-only mode)
- Planning artifact: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/API/{ApiName}.md`
  MUST contain a `## Required Items` block.
- Current code state on disk.

If any required input missing => `ACC_ERR_REAUDIT_INPUT_MISSING`.

## [03] Verification Catalog (API File Baseline)
Reaudit's PRIMARY scoring source is Planning's `## Required Items`. The
following baseline checks are universal for any WebSDK API file; gaps
without a matching RI are recorded as `Observation` (severity `info`).

### 3.1 File Skeleton
- Single `<?php` at start; no `?>` close tag.
- `Access();` near top (canonical).
- Full AliensStyle file CodeHeader present:
  - First line of header banner: `# Copyright (c) A|iens. All rights reserved.`
  - Author MUST be exactly `AlienCyborg`.
- File CodeFooter present.

### 3.2 API Surface
- API entry point exists and matches `{ApiName}` naming.
- Input/Output contract documented in header (Inputs[], Outputs).
- Authentication / authorization assumptions stated.
- Validation present at boundary; no unsafe eval/exec.
- Output JSON envelope consistent (`s | c | t | o | starttime | endtime`).

### 3.3 SDK Integration
- Module class loaded via canonical loader (no hardcoded paths).
- DB table references use base names (no `Coinpods_...` hardcoded).
- Helper calls (`Row*`, `UID_Encode`, etc.) result-checked before returning success.
- `$echo` + `d($echo, __LINE__, '...')` discipline on every public path.

### 3.4 Naming Hygiene
- ApiName matches `^[A-Za-z0-9_]+$`.
- Singular Entity Name Rule respected.
- Spelling traps (`oprator`, `Historys`) flagged as `Observation`.

## [04] Compliance Matrix Template
See `_Common/Workflow_Singular-Reaudit.md` Section [04].
- Verdict: `compliant` | `noncompliant`
- Append iteration body (do not overwrite previous iteration).

## [05] Verdict Rules
Per common engine [03.3]. Single `OutOfScopeChanges[]` violation => `noncompliant`.

## [06] Loop Back
Per common engine [05]: `noncompliant` => orchestrator loops back to Planning.

## [07] Errors
- `ACC_ERR_REAUDIT_INPUT_MISSING`
- `ACC_ERR_REAUDIT_NONCOMPLIANCE`
- `ACC_ERR_REAUDIT_MAX_ITERATIONS` (only if positive bound set)
- `ACC_ERR_SCOPE_DEVIATION`

## Changelog
- 26.00.00 (2026-04-18): Initial Reaudit child for `WebSDK_Module_API`. Author: AlienCyborg.
