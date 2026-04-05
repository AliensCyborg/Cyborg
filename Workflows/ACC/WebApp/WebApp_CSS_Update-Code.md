---
WorkflowId: WebApp_CSS_Update-Code
Type: Workflow_Singular-Code
Domain: WebApp
ParentWorkflowId: WebApp_CSS_Update
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/ACC.Safety.md
NonNegotiables:
  - "Modify-only"
  - "No deletes"
  - "Preserve existing sections when possible"
...

# [0] Purpose
Existing `User/css/0.css` ko modify-only update karna.

Rules:
- Safe additive changes preferred
- If structure differs, keep user's existing ordering but inject missing blocks
- No destructive resets (avoid global `*{}` wipes)
