---
WorkflowId: PySDK_Module_Cron-Planning
WorkflowName: PySDK Module Cron Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_Cron
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/CronName"
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Cron/{CronNameOrIndex}.plan.md"
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
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/cron/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/cron/Product_OrdersPayment.py"
---

# PySDK_Module_Cron-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Target Interpretation
- If `Targets` has no `/` => module-only baseline:
  - CronNameOrIndex = `index`
  - Plan creates/updates `cron/index.py` placeholder (optional) and records folder baseline.
- If `Targets` is `Module/CronName` => cron job planning.

---

## Path Resolution
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`
- `{CronFolder} = {ModuleRoot}/cron/`
- `{CronIndex} = {CronFolder}/index.py`
- `{CronFile} = {CronFolder}/{CronName}.py` (item mode)

---

## Dependency Gate
- `{ModuleRoot}` must exist.
- `{CronFolder}` may be created by Code workflow (non-destructive).

---

## PlanMode
- Module-only: Create/Update based on `{CronIndex}` existence
- Item mode: Create/Update based on `{CronFile}` existence

Planner MUST record:
- exact target file path
- idempotency requirement (cron must be safe to rerun)
- schedule assumptions (if provided)
- side effects and logging rules

---

## Save
- `/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Cron/{CronNameOrIndex}.plan.md`
