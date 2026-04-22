---
WorkflowId: WebApp_Structure_Update
Type: Workflow_Plural
Domain: WebApp
Scope: Modify-only scaffolding updates (Planning + Code + Documentation)
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
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Structure_Update"
    - Targets: "AppName, AppName, ..."
    - Description: "REQUIRED"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "Modify-only; missing target => error"
  - "No delete operations"
  - "DescriptionRequired"
  - "Ensure required entry/aggregator files exist (index/layout/css/js/component)"
  - "Ensure required base CSS exists (User/css/0.css)"
...

# [0] Purpose
Existing app structure ko planner ke mutabiq update karna (no deletes).

# [1] Child Chain
1) `WebApp_Structure_Update-Planning`
2) `WebApp_Structure_Update-Code`
2.5) `WebApp_Structure_Update-Reaudit` (loop back to Planning if `noncompliant`; see `_Common/Workflow_Plural.md` [05A])
3) `WebApp_Structure_Update-Documentation`
