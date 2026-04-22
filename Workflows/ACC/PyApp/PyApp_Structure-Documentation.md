---
WorkflowId: PyApp_Structure-Documentation
Type: Workflow_Singular-Documentation
Domain: PyApp
ParentWorkflowId: PyApp_Structure
Status: Draft
Version: 26.00.03
LastUpdated: 2026-01-19
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
Refs:
  - Workflows/ACC/_Refs/ACC.Output.md
Outputs:
  - DocFile: "/Aliens/Docs/PyApp/{AppName}/PyApp_Structure.md"
  - "Scaffold summary"
...

# [0] Purpose
Scaffold completion ki **enterprise documentation** generate karna.

Doc output path (SSOT):
- `/Aliens/Docs/PyApp/{AppName}/PyApp_Structure.md`

# [0A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if scaffold evidence missing
- Security/Performance/Observability/Testing sections required

Hard rule:
- Ye workflow **short summary** produce nahi karega. 19-section structure mandatory hai; lekin scope sirf structure/scaffold tak tight rahe.

Must mention:
- Required entry/aggregator files ensured.
- Required base CSS ensured: `User/css/0.css`.
- Naming enforced: Screen/Section/Component/CSS basenames use `AppName.` dot prefix (no double prefix).
