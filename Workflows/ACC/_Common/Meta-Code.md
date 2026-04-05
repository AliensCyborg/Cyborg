---
WorkflowId: Meta-Code
Type: Workflow_Singular-Code
Domain: Common
ParentWorkflowId: Meta
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-16
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
NonNegotiables:
  - "No product code generation"
  - "No delete operations"
...

# [0] Purpose
Meta step me "Code" ka matlab: run-level deterministic artifacts.

Allowed outputs (only when needed by runner):
- planner row status normalization
- run record pointer updates

Forbidden:
- WebApp/WebOS/PyOS actual product files generation
