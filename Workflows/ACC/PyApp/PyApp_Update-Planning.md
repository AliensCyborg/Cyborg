---
WorkflowId: PyApp_Update-Planning
WorkflowName: PyApp Update Planning
Type: Workflow_Plural
Domain: PyApp
Scope: Create update plan based on update research outputs
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Update-Planning"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/"
    FileName: "PyApp_Update-Planning.md"
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
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
...

# [0] Purpose
`Workflow("PyApp_Update-Planning", "{AppName}", "{Description}")`

Create a complete update plan for an existing app based on `PyApp_Update-Research` outputs.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

# [1.1] ExampleFiles (Read-Only References)
Read these example files to match existing update planning patterns (no guessing):
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/layout/AlienCyborg.Nav.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Product.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/section/AlienCyborg.Product_Hero.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css`
- `/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/icon/Users.svg`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/py/index.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/js/index.js` (primary)
- `/Aliens/PySDK/Py.26.00.00/Module/Product/js/index.py` (legacy/placeholder in some modules)
- `/Aliens/PySDK/Py.26.00.00/Module/Product/sql/index.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/api/Product_OrderNew.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/cron/Product_OrdersPayment.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/webhook/ProductStatus.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Product/screen/Product.py`

---

# [2] Dependencies (Hard Gate)
Requires update research artifact:
- `/Aliens/.Alien/{Developer}/Research/PyApp/{AppName}/PyApp_Update-Research.md`

If missing:
- ErrorCode: `WF_RESEARCH_REQUIRED`

---

# [3] Planning Scope (Minimum Required)
Update planning MUST define:
- Update goals (what changes) + success criteria
- Constraints + non-goals (what must NOT change)
- Targets to update (explicit list)
  - Screens to update (preferred workflow: `PyApp_Screen_Update`)
  - Sections to update (preferred workflow: `PyApp_Section_Update`)
  - Components to update (preferred workflow: `PyApp_Component_Update`)
- Dependency impact plan (when a screen update requires section changes, etc.)
- Risk mitigation plan (small steps, test points, rollback notes)
- Output artifacts and ownership

---

# [4] Save Outputs
PlanningOut (markdown):
- `/Aliens/.Alien/{Developer}/Planning/PyApp/{AppName}/PyApp_Update-Planning.md`

---

# [5] Errors
- `WF_RESEARCH_REQUIRED`
- `WF_PERMISSION_DENIED`
