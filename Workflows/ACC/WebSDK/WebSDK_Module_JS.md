---
WorkflowId: WebSDK_Module_JS
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module"
Scope: Generate/update module JS bundle (js/index.*)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates WebSDK module JS bundle.
  It orchestrates Planning -> Code -> Documentation per Module target.
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_JS-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_JS-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_JS-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated list. Each item must be a ModuleName (NO slash)."
  - Name: Description
    Type: string
    Required: false
    Notes: "JS requirements (exports, API clients, UI helpers), any language."
Defaults:
  WebSDKVersion: "latest"
  VersionFallback: "PHP.26.00.00"
  NoDelete: true
  ApprovalMode: "from /Aliens/manifest.json"
PermissionsRequested:
  ProdWrite: false
  DbTouch: "no"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Planning", "Code", "Documentation"]
FailureStrategy:
  FailFast: false
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/WebSDK/"
---

# WebSDK_Module_JS.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_JS` ek **Plural** workflow hai jo module ke JS artifacts generate/update karta hai.

Boundary (SSOT):
- Yeh JS **Module** JS hai (domain/content; DB/API linkage expected).
- Module JS may use WebOS engines/libraries.
- Module JS may use other WebSDK modules.
- WebOS engines/libraries must not depend on modules.

Example:
- `Workflow("WebSDK_Module_JS", "Product", "Product module JS bundle clean + exportable banao")`

---

## Target Grammar (Strict)
- `ModuleName` (NO slash)

---

## Path Contract (Observed)
Modules me JS folder generally yeh patterns use karta hai:
- Preferred: `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/js/index.js`
- Legacy/placeholder (some modules): `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/js/index.php`

Module JS contract (preferred):
- `index.js` me ek class define ho sakti hai aur usko export kiya jata hai.
- Isi file me event listeners/actions bhi ho sakte hain.

This workflow MUST:
- Detect what exists
- Prefer non-breaking updates
- Avoid deleting either file

---

## Child Workflow Calls (per module)
1) `WebSDK_Module_JS-Planning`
2) `WebSDK_Module_JS-Code`
3) `WebSDK_Module_JS-Documentation`
