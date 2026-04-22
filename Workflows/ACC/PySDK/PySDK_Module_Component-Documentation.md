---
WorkflowId: PySDK_Module_Component-Documentation
WorkflowName: PySDK Module Component Documentation Generator
Type: Workflow_Singular-Documentation
ParentWorkflowId: PySDK_Module_Component
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "Module OR Module/ComponentName"
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/Component/{ComponentNameOrIndex}.plan.md"
Produces:
  - Documentation: "/Aliens/Docs/PySDK/Module/{Module}/Component/{ComponentNameOrIndex}.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
    - "Workflows/ACC/_Refs/PySDK.md"
NonNegotiables:
  - DocsLocationFixed: "/Aliens/Docs/PySDK"
  - NoDelete: true
---

# PySDK_Module_Component-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## [00B] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if anything is missing
- Security/Performance/Observability/Testing sections required

Rule:
- Any local “Must include” list is additive; it MUST be mapped into the 19-section structure (must not shorten/override SSOT).

## Role
Component docs generate karo.

Must include:
- Purpose
- Public API / inputs
- Render/output notes
- Dependencies

Save:
- `/Aliens/Docs/PySDK/Module/{Module}/Component/{ComponentNameOrIndex}.md`
