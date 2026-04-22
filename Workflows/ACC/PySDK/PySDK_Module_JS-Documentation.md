---
WorkflowId: PySDK_Module_JS-Documentation
WorkflowName: PySDK Module JS Documentation Generator
Type: Workflow_Singular-Documentation
ParentWorkflowId: PySDK_Module_JS
Domain: PySDK
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  - Targets: "ModuleName, ModuleName2, ..."
  - Description: "Any human language requirement"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/PySDK/Module/{Module}/JS/index.plan.md"
Produces:
  - Documentation: "/Aliens/Docs/PySDK/Module/{Module}/JS/index.md"
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

# PySDK_Module_JS-Documentation.md

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
Module JS docs generate karo.

Must include:
- File locations
- Export surface
- Usage snippet
- Change log notes (if update)

Additional must include (non-negotiable):
- Module class contract:
  - JS file `js/index.js` must define class `{Module}`
  - Default export required; named export recommended
- Integration note:
  - Module Python entry: `/Aliens/PySDK/{Python.Version}/Module/{Module}/py/index.py`
  - Describe how JS and Python module class responsibilities stay separated but aligned by module name

Save:
- `/Aliens/Docs/PySDK/Module/{Module}/JS/index.md`
