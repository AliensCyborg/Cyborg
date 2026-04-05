---
WorkflowId: WebApp-Audit
WorkflowName: WebApp Audit
Type: Workflow_Plural
Domain: WebApp
Scope: Audit the entire WebApp after development completes
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp-Audit"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Audit/WebApp/{AppName}/"
    FileName: "WebApp-Audit.md"
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
ExampleFiles: []
...

# [0] Purpose
`Workflow("WebApp-Audit", "{AppName}", "{Description}")`

Audit the entire app after development completes.

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
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.php` (legacy/placeholder in some modules)
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/php/index.php` (WebApp baseline module)
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/js/index.js` (WebApp baseline JS; class `User` exported)
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/Product_OrderNew.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/cron/Product_OrdersPayment.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/webhook/ProductStatus.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/screen/Product.php`

---

# [2] Dependencies
Requires planner CSV with all tasks completed:
- `/Aliens/Project/{Developer_Username}/{AppName}_Planner.csv`

If any task is still `Status=0`:
- ErrorCode: `WF_DEVELOPMENT_INCOMPLETE`

---

# [3] Audit Scope (Minimum Required)
Audit must include:
- Planner coverage: every task audited
- UI/CSS SSOT compliance (ACC UI rules)
- BackendGuard + default dataset policy compliance
- Security basics (auth/session, input validation, PII)
- Performance basics (pagination, payload size)
- Docs completeness

---

# [4] Save Outputs
AuditOut:
- `/Aliens/.Alien/{Developer}/Audit/WebApp/{AppName}/WebApp-Audit.md`

Optional:
- `/Aliens/.Alien/{Developer}/Audit/WebApp/{AppName}/Findings.csv`

---

# [5] Errors
- `WF_DEVELOPMENT_INCOMPLETE`
- `WF_PERMISSION_DENIED`
