---
WorkflowId: Meta-Planning
Type: Workflow_Singular-Planning
Domain: Common
ParentWorkflowId: Meta
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-16
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
Inputs:
  TaskRow (from Planner):
    - Worklflow: "Meta"
    - Name: "__PlannerMeta__ (recommended)"
    - Description: "REQUIRED"
Outputs:
  - "Planner schema validation report (in-memory for the run)"
  - "Normalized meta defaults (Version/Theme/Roots)"
NonNegotiables:
  - "Fail fast on missing/empty Description"
...

# [0] Purpose
Planner meta ki validation + deterministic defaults.

---

# [1] Mandatory Checks
## 1.1 Description Required
- Empty => error `ACC_ERR_DESCRIPTION_REQUIRED`

## 1.2 CSV Schema Gate (when running via planner)
If execution context provides planner CSV headers, validate:
- must include required columns (minimum):
  - `ID, Name, Worklflow, Description, Status`
- if schema invalid => `ACC_ERR_CSV_SCHEMA_INVALID`

---

# [2] Deterministic Defaults
When app context is known (AppName):
- Version default: `26.00.00`
- Theme default: `{AppName}_Basic`
- PlanningRoot: `/Aliens/.Alien/{Developer}/Planning/`
- RunsRoot: `/Aliens/.Alien/{Developer}/Runs/`

---

# [3] No Side Effects
This step only plans/validates; no filesystem writes.
