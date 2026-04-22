---
WorkflowId: PySDK_Module_Screen-Planning
WorkflowName: PySDK Module Screen Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_Screen
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/ScreenName"
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Screen/{ScreenNameOrIndex}.plan.md"
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
---

# PySDK_Module_Screen-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Target Interpretation
- Module-only (no `/`) => baseline (ScreenNameOrIndex=`index`)
- Module/ScreenName => specific file

---

## Paths
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`
- `{ScreenFolder} = {ModuleRoot}/screen/`
- `{ScreenFile} = {ScreenFolder}/{ScreenName}.py`

---

## Planner MUST record
- Screen routing / entry assumptions
- Data sources + permissions
- Render layout + component usage

Save:
- `/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Screen/{ScreenNameOrIndex}.plan.md`
