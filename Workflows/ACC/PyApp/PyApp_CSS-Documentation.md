---
WorkflowId: PyApp_CSS-Documentation
Type: Workflow_Singular-Documentation
Domain: PyApp
ParentWorkflowId: PyApp_CSS
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-25
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
Refs:
  - Workflows/ACC/_Refs/ACC.Output.md
  - Workflows/ACC/_Refs/ACC.Safety.md
NonNegotiables:
  - "Docs must explain tokens + usage conventions"
  - "No secrets/logs"
...

# [0] Purpose
Global PyApp CSS (0.css) documentation generate/update karna.

Docs must cover:
- Where app-level CSS lives
- Token dictionary (base vs app-level)
- How to use in Components/Sections/Screens/Layouts

---

# [1] Doc Location (SSOT)
- `/Aliens/Docs/PyApp/{AppName}/CSS/{AppName}.Global.md`

---

# [2] Required Sections
Docs MUST include:
1) Overview
2) Files
3) Token reference
4) Utility classes
5) Overlay patterns
6) Do/Don’t rules
