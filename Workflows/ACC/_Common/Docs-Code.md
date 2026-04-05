---
WorkflowId: Docs-Code
Type: Workflow_Singular-Code
Domain: Common
ParentWorkflowId: Docs
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-16
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Output.md
NonNegotiables:
  - "Docs only (no product code)"
  - "No delete operations"
...

# [0] Purpose
Docs artifact produce karna (Markdown).

Rules:
- enterprise-grade structure
- deterministic headings
- no placeholders
