---
WorkflowId: PyApp-Audit
WorkflowName: PyApp Audit
Type: Workflow_Plural
Domain: PyApp
Scope: Audit the entire PyApp after development completes
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp-Audit"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Audit/PyApp/{AppName}/"
    FileName: "PyApp-Audit.md"
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
`Workflow("PyApp-Audit", "{AppName}", "{Description}")`

Audit the entire app after development completes.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

# [1.1] ExampleFiles (Read-Only References)
These files demonstrate the patterns this workflow should recognize:
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
- `/Aliens/.Alien/{Developer}/Audit/PyApp/{AppName}/PyApp-Audit.md`

Optional:
- `/Aliens/.Alien/{Developer}/Audit/PyApp/{AppName}/Findings.csv`

---

# [5] Errors
- `WF_DEVELOPMENT_INCOMPLETE`
- `WF_PERMISSION_DENIED`
