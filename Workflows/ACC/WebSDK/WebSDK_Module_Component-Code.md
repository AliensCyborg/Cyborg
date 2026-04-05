---
WorkflowId: WebSDK_Module_Component-Code
WorkflowName: WebSDK Module Component Code Generator
Type: Workflow_Singular-Code
ParentWorkflowId: WebSDK_Module_Component
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/ComponentName"
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/Component/{ComponentNameOrIndex}.plan.md"
Produces:
  - ComponentFile: "/Aliens/WebSDK/{PHP.Version}/Module/{Module}/component/{ComponentNameOrIndex}.php"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/WebSDK.md"
NonNegotiables:
  - NoDelete: true
---

# WebSDK_Module_Component-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## Role
PlanningDoc follow karke `component/` me PHP component create/update karo.

Folder rules:
- Create `component/` if missing (non-destructive)

Run record:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`
