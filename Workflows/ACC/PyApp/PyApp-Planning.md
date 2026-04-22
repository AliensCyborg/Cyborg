---
WorkflowId: PyApp-Planning
WorkflowName: PyApp Planning
Type: Workflow_Plural
Domain: PyApp
Scope: Create planning artifacts for a new PyApp based on Research
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp-Planning"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/"
    FileName: "PyApp-Planning.md"
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
`Workflow("PyApp-Planning", "{AppName}", "{Description}")`

Create a complete plan for the app based on `PyApp-Research` outputs.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

# [1.1] ExampleFiles (Read-Only References)
Read these example files to match existing patterns (no guessing):
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/icon/Users.svg`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/py/index.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/js/index.js` (primary)
- `/Aliens/PySDK/Py.26.00.00/Module/Product/js/index.py` (legacy/placeholder in some modules)
- `/Aliens/PySDK/Py.26.00.00/Module/User/py/index.py` (PyApp baseline module)
- `/Aliens/PySDK/Py.26.00.00/Module/User/js/index.js` (PyApp baseline JS; class `User` exported)
- `/Aliens/PySDK/Py.26.00.00/Module/Product/sql/index.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product_OrderNew.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/cron/Product_OrdersPayment.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/webhook/ProductStatus.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/screen/Product.py`

---

# [2] Dependencies (Hard Gate)
Requires research artifact:
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/PyApp-Research.md`

If missing:
- ErrorCode: `WF_RESEARCH_REQUIRED`

---

# [3] Planning Scope (Minimum Required)
Planning MUST define:
- App scope + non-goals
- Screen list (SSOT) + sections per screen
- Components list (plural/singular relationships)
- PySDK modules list
- API endpoints list (request/response + error codes)
- PyOS engine needs (if any)
- PyOS_manifest needs for the app:
  - `libraries` (engines/third-party)
  - `modules` (domain modules)
- PyApp JS rule: custom JS in PyApp is forbidden; JS must live in Module/Engine.
- Default module for PyApp JS features: `User` (module-level JS via `PySDK_Module_JS`).
- DB schema buckets (high level)
- Priority strategy (P0/P1/P2) and milestones

---

# [4] Save Outputs
PlanningOut (markdown):
- `/Aliens/.Alien/{Developer}/Planning/PyApp/{AppName}/PyApp-Planning.md`

---

# [5] Errors
- `WF_RESEARCH_REQUIRED`
- `WF_PERMISSION_DENIED`
