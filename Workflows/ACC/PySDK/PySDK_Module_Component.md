---
WorkflowId: PySDK_Module_Component
Type: Workflow_Plural
Domain: PySDK
TargetKind: "Module/Component"
Scope: Generate/update module component Python files under component/
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates PySDK module Python components.
  Supports module-only baseline and item mode (Module/ComponentName).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/PySDK/PySDK_Module_Component-Planning.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Component-Code.md"
  - "Workflows/ACC/PySDK/PySDK_Module_Component-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item is ModuleName OR ModuleName/ComponentName."
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

# PySDK_Module_Component.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`PySDK_Module_Component` module ke `component/` folder me Python components generate/update karta hai.

Examples:
- Baseline: `Workflow("PySDK_Module_Component", "Product", "component folder baseline ensure")`
- Specific: `Workflow("PySDK_Module_Component", "Product/Product_MoneyCard", "MoneyCard component ko improve")`

---

## Target Grammar
Allowed:
- `ModuleName`
- `ModuleName/ComponentName`

File mapping:
- `{ModuleRoot}/component/{ComponentName}.py`
