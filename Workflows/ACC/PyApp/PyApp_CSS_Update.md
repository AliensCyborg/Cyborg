---
WorkflowId: PyApp_CSS_Update
Type: Workflow_Plural
Domain: PyApp
Scope: Update existing PyApp App-Level CSS (modify-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Errors.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_CSS_Update"
    - Targets: "AppName, AppName, ..."
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Update-only: missing targets => error"
  - "Modify-only: do not overwrite from scratch"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Existing global CSS design system (User/css/0.css) ko **modify-only** update karna.

Example:
- Workflow( "PyApp_CSS_Update", "Trip", "Adjust brand colors + add new layout utilities" )

---

# [1] Child Chain (Recommended Order)
0) `PyApp_CSS_Update-Audit` (read-only)
1) `PyApp_CSS_Update-Planning`
2) `PyApp_CSS_Update-Code`
3) `PyApp_CSS_Update-Documentation`
