---
WorkflowId: PyApp_Structure
Type: Workflow_Plural
Domain: PyApp
Scope: Deterministic PyApp scaffolding (Planning + Code + Documentation)
Status: Draft
Version: 26.00.03
LastUpdated: 2026-01-19
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Structure"
    - Targets: "AppName, AppName, ..."
    - Description: "REQUIRED; structure constraints"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "DescriptionRequired"
  - "No delete operations"
  - "Create required runtime bootstrapping files + placeholders (no business code)"
  - "Update planner path columns deterministically when running via planner"
...

# [0] Purpose
`PyApp_Structure` ka goal: planner CSV ki base par **folders + empty placeholder files** scaffold karna.

Compatibility note:
- Repo me legacy spec file `Workflows/ACC/PyApp/PyApp-Structure.md` exist karti hai.
- `PyApp_Structure` is ACC-v26 registry workflow id hai (underscore), taa ke planners me naming stable rahe.

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Child Chain (Default Order)
1) `PyApp_Structure-Planning`
2) `PyApp_Structure-Code`
3) `PyApp_Structure-Documentation`

---

# [3] Hard Rules
- This workflow scaffolds structure only.
- No business logic.
- No deletes.
- Deterministic mapping must follow `PyApp_Naming_and_Screens.md`.

Naming rule (NON-NEGOTIABLE):
- Screen/Section/Component/CSS file basenames MUST use dot prefix: `AppName.<Name>`.
- No double prefix: agar basename already `AppName.` se start hota hai to dobara prefix add nahi karna.
- Agar prefix missing ho to add karna mandatory (deterministic).

Bootstrapping allowance (NON-NEGOTIABLE):
- `User/index.py`, `User/layout.py`, `User/css.py`, `User/js.py`, `User/component.py` are **required** entry/aggregator files.
- In Structure workflow, these files may contain **minimal boot code** (Access + AppHead + include layout + directory aggregators).
- These files must not contain domain/business features; that belongs in Screen/Section/Component workflows.

JS rule (NON-NEGOTIABLE):
- PyApp ke andar custom JS likhna allowed nahi.
- `User/js.py` file required hai (compatibility), lekin yeh **local JS code** aggregate/include na kare.
- PyApp ko JS chahiye ho to JS ka home:
  - First priority: PySDK Module (`/Aliens/PySDK/.../Module/{Module}/js/index.js`)
  - Reusable big feature: PyOS Engine

Base styling allowance (NON-NEGOTIABLE):
- `User/css/0.css` is a **required** base styling file.
- It must contain `:root{...}` variables + shared reusable CSS used across the whole app (layout/screen baseline + common utilities), and must not contain business/domain logic.
