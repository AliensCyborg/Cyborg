---
WorkflowId: WebApp-Planning
WorkflowName: WebApp Planning
Type: Workflow_Plural
Domain: WebApp
Scope: Create planning artifacts for a new WebApp based on Research
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp-Planning"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/"
    FileName: "WebApp-Planning.md"
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
  - Workflows/ACC/_Refs/ACC.Paths.md
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("WebApp-Planning", "{AppName}", "{Description}")`

Create a complete plan for the app based on `WebApp-Research` outputs.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

# [1.1] ExampleFiles (Read-Only References)
Read these example files to match existing patterns (no guessing):
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

# [2] Dependencies (Hard Gate)
Requires research artifact:
- `/Aliens/.Alien/{Developer}/Research/WebApp/{AppName}/WebApp-Research.md`

If missing:
- ErrorCode: `WF_RESEARCH_REQUIRED`

---

# [3] Planning Scope (Minimum Required)
Planning MUST define:
- App scope + non-goals
- Screen list (SSOT) + sections per screen
- Components list (plural/singular relationships)
- WebSDK modules list
- API endpoints list (request/response + error codes)
- WebOS engine needs (if any)
- WebOS_manifest needs for the app:
  - `libraries` (engines/third-party)
  - `modules` (domain modules)
- WebApp JS rule: custom JS in WebApp is forbidden; JS must live in Module/Engine.
- Default module for WebApp JS features: `User` (module-level JS via `WebSDK_Module_JS`).
- DB schema buckets (high level)
- Priority strategy (P0/P1/P2) and milestones

---

# [4] Save Outputs
PlanningOut (markdown):
- `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/WebApp-Planning.md`

---

# [5] Errors
- `WF_RESEARCH_REQUIRED`
- `WF_PERMISSION_DENIED`
