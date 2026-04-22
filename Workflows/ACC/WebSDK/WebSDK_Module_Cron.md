---
WorkflowId: WebSDK_Module_Cron
Type: Workflow_Plural
Domain: WebSDK
TargetKind: "Module/Cron"
Scope: Generate/update module cron jobs under cron/
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Summary: >
  Plural workflow that generates/updates WebSDK module cron files.
  Supports module-only mode (ensure cron folder baseline) and item mode (Module/CronName).
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "Workflows/ACC/WebSDK/WebSDK_Module_Cron-Planning.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Cron-Code.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Cron-Reaudit.md"
  - "Workflows/ACC/WebSDK/WebSDK_Module_Cron-Documentation.md"
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Notes: "Comma-separated. Each item is ModuleName OR ModuleName/CronName."
  - Name: Description
    Type: string
    Required: false
    Notes: "Cron behavior requirements and schedule notes."
Defaults:
  WebSDKVersion: "latest"
  VersionFallback: "PHP.26.00.00"
  NoDelete: true
  ApprovalMode: "from /Aliens/manifest.json"
PermissionsRequested:
  ProdWrite: false
  DbTouch: "no"
  NoDelete: true
Execution:
  Mode: "orchestrator"
  Order: ["Planning", "Code", "Reaudit", "Documentation"]
  MaxReauditIterations: 0
FailureStrategy:
  FailFast: false
Output:
  RunRecordsRoot: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/"
  PlanningRoot: "/Aliens/.Alien/{Developer_Username}/Planning/"
  DocsRoot: "/Aliens/Docs/WebSDK/"
---

# WebSDK_Module_Cron.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## Purpose
`WebSDK_Module_Cron` module ke cron scripts generate/update karta hai.

Examples:
- Baseline (module-only): `Workflow("WebSDK_Module_Cron", "Teacher", "Cron folder baseline ensure karo")`
- Specific cron job: `Workflow("WebSDK_Module_Cron", "Product/Product_OrdersPayment", "Payment cron ko safe + idempotent banao")`

---

## Target Grammar
Allowed:
- `ModuleName` (module-only baseline)
- `ModuleName/CronName` (single cron job file)

File mapping:
- ModuleRoot: `/Aliens/WebSDK/{PHP.Version}/Module/{Module}/`
- Cron folder: `{ModuleRoot}/cron/`
- Cron file: `{ModuleRoot}/cron/{CronName}.php`

---

## Orchestration Contract
For each target item:
- Call in order:
  1) `WebSDK_Module_Cron-Planning`
  2) `WebSDK_Module_Cron-Code`
  3) `WebSDK_Module_Cron-Documentation`

No direct code generation in this file.
