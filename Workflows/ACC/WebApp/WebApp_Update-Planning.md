---
WorkflowId: WebApp_Update-Planning
WorkflowName: WebApp Update Planning
Type: Workflow_Plural
Domain: WebApp
Scope: Create update plan based on update research outputs
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Update-Planning"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/"
    FileName: "WebApp_Update-Planning.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
  - "Update-only; no scope expansion"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.php"
  - "/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
...

# [0] Purpose
`Workflow("WebApp_Update-Planning", "{AppName}", "{Description}")`

Create a complete update plan for an existing app based on `WebApp_Update-Research` outputs.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

# [1.1] ExampleFiles (Read-Only References)
Read these example files to match existing update planning patterns (no guessing):
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.php`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css`
- `/Aliens/WebApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/icon/Users.svg`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/php/index.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.js` (primary)
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/js/index.php` (legacy/placeholder in some modules)
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/sql/index.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/api/Product_OrderNew.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/cron/Product_OrdersPayment.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/webhook/ProductStatus.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Product/screen/Product.php`

---

# [2] Dependencies (Hard Gate)
Requires update research artifact:
- `/Aliens/.Alien/{Developer}/Research/WebApp/{AppName}/WebApp_Update-Research.md`

If missing:
- ErrorCode: `WF_RESEARCH_REQUIRED`

---

# [3] Planning Scope (Minimum Required)
Update planning MUST define:
- Update goals (what changes) + success criteria
- Constraints + non-goals (what must NOT change)
- Targets to update (explicit list)
  - Screens to update (preferred workflow: `WebApp_Screen_Update`)
  - Sections to update (preferred workflow: `WebApp_Section_Update`)
  - Components to update (preferred workflow: `WebApp_Component_Update`)
- Dependency impact plan (when a screen update requires section changes, etc.)
- Risk mitigation plan (small steps, test points, rollback notes)
- Output artifacts and ownership

---

# [4] Save Outputs
PlanningOut (markdown):
- `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/WebApp_Update-Planning.md`

---

# [5] Errors
- `WF_RESEARCH_REQUIRED`
- `WF_PERMISSION_DENIED`
