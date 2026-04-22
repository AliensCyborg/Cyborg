---
WorkflowId: PySDK_Module_Webhook-Code
WorkflowName: PySDK Module Webhook Code Generator
Type: Workflow_Singular-Code
ParentWorkflowId: PySDK_Module_Webhook
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/WebhookName"
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Webhook/{WebhookNameOrIndex}.plan.md"
Produces:
  - WebhookFile: "/Aliens/PySDK/{Python.Version}/Module/{Module}/webhook/{WebhookNameOrIndex}.py"
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

# PySDK_Module_Webhook-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## Role
PlanningDoc ko follow karke webhook handler file create/update karo.

Security baseline (hard):
- Validate request signature if applicable
- Validate content-type + size limits
- Enforce idempotency (retries)
- Do not log secrets

Folder rules:
- Create `webhook/` folder if missing (non-destructive)

Run record:
- `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`
