---
WorkflowId: PySDK_Module_Component-Planning
WorkflowName: PySDK Module Component Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_Component
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/ComponentName"
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Component/{ComponentNameOrIndex}.plan.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/PySDK.md"
NonNegotiables:
  - NoDelete: true
  - DefaultVersionFallback: "Py.26.00.00"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/component/Product_MoneyCard.py"
---

# PySDK_Module_Component-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Target Interpretation
- Module-only (no `/`) => baseline (ComponentNameOrIndex=`index`)
- Module/ComponentName => specific file

---

## Paths
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`
- `{ComponentFolder} = {ModuleRoot}/component/`
- `{ComponentFile} = {ComponentFolder}/{ComponentName}.py`

---

## Planner MUST record
- Component responsibility + inputs/outputs
- Dependencies on other modules/classes
- Rendering expectations (HTML/CSS hooks)

Save:
- `/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Component/{ComponentNameOrIndex}.plan.md`
