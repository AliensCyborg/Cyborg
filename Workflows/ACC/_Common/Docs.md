---
WorkflowId: Docs
Type: Workflow_Plural
Domain: Common
Scope: Documentation generation workflow (Planning + Code + Documentation)
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
    - WorkflowId: "Docs"
    - Targets: "App/DocName, App/DocName, ..."
    - Description: "REQUIRED; doc requirements + constraints"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "DescriptionRequired (empty => fail)"
  - "No delete operations"
  - "StrictScopeOnly (no extra work)"
...

# [0] Purpose
`Docs` workflow ka goal: cross-ecosystem documentation produce karna.

Examples:
- `Workflow("Docs", "B-Commerce/README", "Write enterprise-grade README")`

This workflow is intentionally **cross-ecosystem**, so no `Web*` prefix.

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Child Chain (Default Order)
1) `Docs-Planning`
2) `Docs-Code`
3) `Docs-Documentation`
