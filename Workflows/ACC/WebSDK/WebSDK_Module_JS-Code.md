---
WorkflowId: WebSDK_Module_JS-Code
WorkflowName: WebSDK Module JS Code Generator
Type: Workflow_Singular-Code
ParentWorkflowId: WebSDK_Module_JS
Domain: WebSDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "ModuleName, ModuleName2, ..."
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{Module}/JS/index.plan.md"
Produces:
  - JsFile: "/Aliens/WebSDK/{PHP.Version}/Module/{Module}/js/index.js"
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
  - Approval: "Follow /Aliens/manifest.json"
---

# WebSDK_Module_JS-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## Role
PlanningDoc ko follow karke module JS file create/update karo.

Rules:
- Do not delete `js/index.php` if it exists.
- Prefer updating the chosen primary JS file from plan.
- Create `js/` folder if missing (non-destructive).

---

## Minimum output expectations (when generating index.js)

Non-negotiable JS module contract (when generating/updating `js/index.js`):
- File must define a class named exactly `{Module}`.
- The class MUST be exported (default export required).
- Named export is recommended for flexibility.

Recommended export pattern (ESM):
- `export default class {Module} { ... }`
- `export { {Module} }`

Integration expectations:
- This JS file is the module-level JS surface for the system (WebApps must NOT embed custom JS inside WebApp files).
- Keep exports deterministic and stable; avoid implicit globals.
- Avoid console noise in production unless explicitly required.

Safety defaults:
- No secrets/tokens in code.
- No network calls without explicit endpoints + error handling + timeouts.

---

## Run record
Write: `/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md`
