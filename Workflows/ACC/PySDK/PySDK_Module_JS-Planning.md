---
WorkflowId: PySDK_Module_JS-Planning
WorkflowName: PySDK Module JS Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: PySDK_Module_JS
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "ModuleName, ModuleName2, ..."
  - Description: "Any human language requirement"
Outputs:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/JS/index.plan.md"
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
    - "Workflows/ACC/_Refs/PySDK.md"
NonNegotiables:
  - NoDelete: true
  - DefaultVersionFallback: "Py.26.00.00"
  - Approval: "Follow /Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/js/index.js"
  - "/Aliens/PySDK/Py.26.00.00/Module/Teacher/js/index.py"
---

# PySDK_Module_JS-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## Target Rule
- Only `ModuleName` allowed.
- If `/` present => fail `ACC_ERR_TARGET_INVALID`.

---

## Resolve paths
- `{ModuleRoot} = /Aliens/PySDK/{Python.Version}/Module/{Module}/`
- `{JsFolder} = {ModuleRoot}/js/`
- `{PhpFolder} = {ModuleRoot}/py/`
- `{PhpIndexPhp} = {PhpFolder}/index.py` (module Python class entry)
- Candidate files:
  - `{JsIndexJs} = {JsFolder}/index.js`
  - `{JsIndexPhp} = {JsFolder}/index.py`

Planner MUST record:
- Existing file(s)
- Chosen primary target file (prefer existing; else create `index.js`)
- Python integration expectation:
  - Whether `{PhpIndexPhp}` exists
  - JS class name MUST match `{Module}` and should be designed to work alongside the Python module class (same module name).

---

## PlanMode
- If chosen target exists => Update
- Else => Create

---

## JS Contract (Non-Negotiable)
When the chosen primary file is `index.js`, the plan MUST require these code-level invariants:
- A class named exactly `{Module}` exists in the file.
- That class is exported (default export required; named export recommended).

Recommended export pattern (ESM):
- `export default class {Module} { ... }`
- `export { {Module} }`

PyApp usage baseline:
- If the PyApp needs custom JS features, the default place is `Module=User`:
  - `/Aliens/PySDK/{Python.Version}/Module/User/py/index.py`
  - `/Aliens/PySDK/{Python.Version}/Module/User/js/index.js`

---

## Save
- `/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/JS/index.plan.md`
