---
WorkflowId: PySDK_Module_Screen
Type: Workflow_Plural
Domain: PySDK
TargetKind: "Module/Screen"
Scope: Generate/update module screen Python files under screen/
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates PySDK module Python screens.
  Supports module-only baseline and item mode (Module/ScreenName).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/PySDK/PySDK_Module_Screen-Planning.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Screen-Code.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Screen-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item is ModuleName OR ModuleName/ScreenName."
  - Name: Description
    Type: string
    Required: false
Defaults:
  PySDKVersion: "latest"
  VersionFallback: "Py.26.00.00"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Planning", "Code", "Documentation"]
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/PySDK/"
---

# PySDK_Module_Screen.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`PySDK_Module_Screen` module ke `screen/` folder me Python screens generate/update karta hai.

Examples:
- Baseline: `Workflow("PySDK_Module_Screen", "Product", "screen folder baseline ensure")`
- Specific: `Workflow("PySDK_Module_Screen", "Product/Product_Orders", "Orders screen implement")`

---

## Target Grammar
Allowed:
- `ModuleName`
- `ModuleName/ScreenName`

File mapping:
- `{ModuleRoot}/screen/{ScreenName}.py`
