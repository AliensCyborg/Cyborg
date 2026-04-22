---
WorkflowId: WebSDK_Module_Component
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module/Component"
Scope: Generate/update module component PHP files under component/
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates WebSDK module PHP components.
  Supports module-only baseline and item mode (Module/ComponentName).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_Component-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Component-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Component-Reaudit.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Component-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item is ModuleName OR ModuleName/ComponentName."
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

# WebSDK_Module_Component.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_Component` module ke `component/` folder me PHP components generate/update karta hai.

Examples:
- Baseline: `Workflow("WebSDK_Module_Component", "Product", "component folder baseline ensure")`
- Specific: `Workflow("WebSDK_Module_Component", "Product/Product_MoneyCard", "MoneyCard component ko improve")`

---

## Target Grammar
Allowed:
- `ModuleName`
- `ModuleName/ComponentName`

File mapping:
- `{ModuleRoot}/component/{ComponentName}.php`
