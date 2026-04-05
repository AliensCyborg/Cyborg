---
WorkflowId: WebApp_Update-Audit
WorkflowName: WebApp Update Audit
Type: Workflow_Plural
Domain: WebApp
Scope: Audit the app after update development completes
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Update-Audit"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Audit/WebApp/{AppName}/"
    FileName: "WebApp_Update-Audit.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/icon/Users.svg"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.js"  (primary)
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.php" (legacy/placeholder)
...

# [0] Purpose
`Workflow("WebApp_Update-Audit", "{AppName}", "{Description}")`

Audit the app after update development completes.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

# [1.1] ExampleFiles (Read-Only References)
These files demonstrate the patterns this workflow should recognize:
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/icon/Users.svg`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/php/index.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.js` (primary)
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.php` (legacy/placeholder)
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/Product_OrderNew.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/cron/Product_OrdersPayment.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/webhook/ProductStatus.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/screen/Product.php`

---

# [2] Dependencies
Requires update planner CSV with all tasks completed:
- `/Aliens/Project/{Developer_Username}/{AppName}_Update_Planner.csv`

If any task is still `Status=0`:
- ErrorCode: `WF_DEVELOPMENT_INCOMPLETE`

---

# [3] Audit Scope (Minimum Required)
Audit must include:
- Update planner coverage: every update task audited
- UI/CSS SSOT compliance (ACC UI rules)
- BackendGuard + default dataset policy compliance
- Safety: update-only boundaries respected (no deletes, no scope creep)
- Docs completeness (update documentation written where required)

---

# [4] Save Outputs
AuditOut:
- `/Aliens/.Alien/{Developer}/Audit/WebApp/{AppName}/WebApp_Update-Audit.md`

Optional:
- `/Aliens/.Alien/{Developer}/Audit/WebApp/{AppName}/WebApp_Update-Findings.csv`

---

# [5] Errors
- `WF_DEVELOPMENT_INCOMPLETE`
- `WF_PERMISSION_DENIED`
