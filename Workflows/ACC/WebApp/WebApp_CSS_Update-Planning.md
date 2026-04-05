---
WorkflowId: WebApp_CSS_Update-Planning
Type: Workflow_Singular-Planning
Domain: WebApp
ParentWorkflowId: WebApp_CSS_Update
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
Inputs:
  Target:
    - Format: "AppName"
  Description: "REQUIRED"
Outputs:
  - "Update contract (what changes, what stays)"
NonNegotiables:
  - "DescriptionRequired"
  - "Modify-only"
  - "No deletes"
...

# [0] Purpose
Existing `User/css/0.css` ko update karne ka plan:
- which tokens will change
- which utilities will be added
- backward compatibility constraints

Hard rule:
- base keys are stable (do not rename/remove)
