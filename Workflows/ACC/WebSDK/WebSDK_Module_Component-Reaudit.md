---
WorkflowId: WebSDK_Module_Component-Reaudit
WorkflowName: WebSDK Module Component (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_Component
Domain: WebSDK
TargetKind: "Module/Component"
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
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Component/"
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
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
---

# WebSDK_Module_Component-Reaudit

## [00A] Universal Includes
1) Workflow_Universal.md  2) Workflow_Singular.md  3) Workflow_Singular-Reaudit.md

## [00] Purpose
Post-Code verification for `WebSDK_Module_Component`. Read-only on Code.

## [01] Targets
- Format: `ModuleName` OR `ModuleName/ComponentName`
- File: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/component/{ComponentName}.php`

## [02] Inputs
ReauditMode = `create`. Pre-Code Audit optional.
- Description (may be empty)
- Planning artifact `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Component/{ComponentName}.md` MUST contain `## Required Items`.
- Current code state on disk.

## [03] Verification Catalog (Component Baseline)
### 3.1 File Skeleton
- Single `<?php`; no `?>` close tag; `Access();` near top.
- AliensStyle CodeHeader present; first banner line: `# Copyright (c) A|iens. All rights reserved.`
- Author = `AlienCyborg`. CodeFooter present.

### 3.2 Component Surface
- Component class/function naming matches `{ComponentName}`.
- Inputs documented; safe defaults; output structure consistent.
- No direct DB write from component (delegate to module class).
- Theme + section comments present.

### 3.3 Style + Logging
- `$echo` + `d()` discipline on every public path.
- Spelling traps flagged as `Observation`.

## [04] Compliance Matrix + [05] Verdict + [06] Loop-back + [07] Errors
See `_Common/Workflow_Singular-Reaudit.md`.

## Changelog
- 26.00.00 (2026-04-18): Initial. Author: AlienCyborg.
