---
WorkflowId: WebSDK_Module_Webhook-Planning
WorkflowName: WebSDK Module Webhook Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: WebSDK_Module_Webhook
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/WebhookName"
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/Webhook/{WebhookNameOrIndex}.plan.md"
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
    - "Workflows/ACC/_Refs/WebSDK.md"
NonNegotiables:
  - NoDelete: true
  - DefaultVersionFallback: "PHP.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/webhook/ProductStatus.php"
---

# WebSDK_Module_Webhook-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Target Interpretation
- Module-only (no `/`) => baseline (WebhookNameOrIndex=`index`)
- Module/WebhookName => specific webhook file

---

## Paths
- `{ModuleRoot} = /Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
- `{WebhookFolder} = {ModuleRoot}/webhook/`
- `{WebhookIndex} = {WebhookFolder}/index.php` (optional baseline)
- `{WebhookFile} = {WebhookFolder}/{WebhookName}.php`

---

## Planner MUST record
- Source system + verification expectations (signature, shared secret, header allowlist)
- Idempotency rules (webhook retries)
- Response contract (status codes)
- Data writes and safety (avoid duplicate processing)

---

## Save
- `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/Webhook/{WebhookNameOrIndex}.plan.md`
