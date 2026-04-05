---
WorkflowId: Meta
Type: Workflow_Plural
Domain: Common
Scope: Planner meta and registry-safe setup (Planning + Code + Documentation)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-16
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/ACC.Output.md
Inputs:
  Workflow():
    - WorkflowId: "Meta"
    - Targets: "(optional)"
    - Description: "REQUIRED; this is the prompt payload"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "DescriptionRequired (empty => fail)"
  - "No delete operations"
  - "StrictScopeOnly (no extra work)"
  - "Deterministic paths only"
...

# [0] Purpose
`Meta` workflow ka goal: **planner meta / execution meta** ko enterprise-grade, deterministic aur auditable banana.

Typical use cases:
- Planner CSV me `__PlannerMeta__` row ko validate + normalize karna.
- Schema/required fields check karna (Description always required).
- Execution metadata (run record pointers, paths, defaults) ko deterministic banana.

This workflow is intentionally **cross-ecosystem** (WebOS, PyOS, DartOS, SharpOS) — isliye is par `Web*` prefix nahi.

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Child Chain (Default Order)
1) `Meta-Planning`
2) `Meta-Code`
3) `Meta-Documentation`

---

# [3] Inputs Rules
## 3.1 Description (NON-NEGOTIABLE)
- `Description` empty/missing => fail fast
- Map to ACC error: `ACC_ERR_DESCRIPTION_REQUIRED`

## 3.2 Targets (Optional)
Targets optional hain. Agar provided hon:
- treat as labels only
- no path resolution by targets in this workflow

---

# [4] Output Contract
`Meta` workflow khud product code generate nahi karta.
Iska output:
- validated meta artifacts (planner meta validation notes)
- deterministic run pointers (where applicable)
- updated planner row status fields (when workflow is executed via planner runner)

---

# [5] Safety + Determinism
- No delete operations
- No guessing
- No ecosystem-specific side effects
