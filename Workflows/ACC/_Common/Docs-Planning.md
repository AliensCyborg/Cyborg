---
WorkflowId: Docs-Planning
Type: Workflow_Singular-Planning
Domain: Common
ParentWorkflowId: Docs
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
  Target:
    - Format: "AppName/DocName"
  Description: "REQUIRED"
Outputs:
  - "Deterministic doc path plan"
NonNegotiables:
  - "DescriptionRequired"
...

# [0] Purpose
Documentation generation ka deterministic plan.

---

# [1] Target Rules
Accepted target format:
- `AppName/DocName`

Doc path resolution (default, unless task overrides):
- `/Aliens/Docs/{AppName}/{DocName}.md`

---

# [2] Description Required
Empty => `ACC_ERR_DESCRIPTION_REQUIRED`
