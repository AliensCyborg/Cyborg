---
WorkflowId: WebSDK_Module_Screen
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module/Screen"
Scope: Generate/update module screen PHP files under screen/
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates WebSDK module PHP screens.
  Supports module-only baseline and item mode (Module/ScreenName).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_Screen-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Screen-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Screen-Reaudit.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Screen-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item is ModuleName OR ModuleName/ScreenName."
  - Name: Description
    Type: string
    Required: false
Defaults:
  WebSDKVersion: "latest"
  VersionFallback: "PHP.26.00.00"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Planning", "Code", "Reaudit", "Documentation"]
  MaxReauditIterations: 0
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/WebSDK/"
---

# WebSDK_Module_Screen.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_Screen` module ke `screen/` folder me PHP screens generate/update karta hai.

Examples:
- Baseline: `Workflow("WebSDK_Module_Screen", "Product", "screen folder baseline ensure")`
- Specific: `Workflow("WebSDK_Module_Screen", "Product/Product_Orders", "Orders screen implement")`

---

## Target Grammar
Allowed:
- `ModuleName`
- `ModuleName/ScreenName`

File mapping:
- `{ModuleRoot}/screen/{ScreenName}.php`
