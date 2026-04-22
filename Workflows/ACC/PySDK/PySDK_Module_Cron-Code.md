---
WorkflowId: PySDK_Module_Cron-Code
WorkflowName: PySDK Module Cron Code Generator
Type: Workflow_Singular-Code
ParentWorkflowId: PySDK_Module_Cron
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/CronName"
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Cron/{CronNameOrIndex}.plan.md"
Produces:
  - CronFile: "/Aliens/PySDK/{Python.Version}/Module/{Module}/cron/{CronNameOrIndex}.py"
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
    - "Workflows/ACC/_Refs/PySDK.md"
NonNegotiables:
  - NoDelete: true
  - Approval: "Follow /Aliens/manifest.json"
---

# PySDK_Module_Cron-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## Role
PlanningDoc ko follow karke cron file create/update karo.

Folder rules:
- Create `{ModuleRoot}/cron/` if missing (non-destructive).
- Do not delete existing cron jobs.

Idempotency (hard rule):
- Cron job must be safe to run multiple times.
- Must log start/end and key decisions.

Run record:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`
