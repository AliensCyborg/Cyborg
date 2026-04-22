---
WorkflowId: PyApp_Component-Documentation
Type: Workflow_Singular-Documentation
Domain: PyApp
Scope: Generate documentation for a PyApp Component
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_Engines.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Component-Documentation"
    - Targets: "App/Component"
    - Description: "Doc constraints (no scope expansion)"
Outputs:
  - DocFile: "/Aliens/Docs/PyApp/{AppName}/Components/{ComponentNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
...

# [0] Purpose
Given `AppName/ComponentName`, yeh workflow component ki **documentation** generate karta hai taaki:
- team ko usage samajh aaye
- premium UI rules consistent rahein
- audit + maintenance easy ho

# [0A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if planning/code info missing
- Security/Performance/Observability/Testing sections required

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
- ComponentFile: `{AppRoot}/User/component/{ComponentNameNormalized}.py`
- CssFile: `{AppRoot}/User/css/{ComponentNameNormalized}.css`
- DocOut: `/Aliens/Docs/PyApp/{AppName}/Components/{ComponentNameNormalized}.md`

Naming normalization (mandatory):
- If `ComponentName` starts with `{AppName}.` => keep
- Else if `ComponentName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{ComponentNameNormalized} = {AppName}.{ComponentName}`
- Use `{ComponentNameNormalized}` for `ComponentFile`, `CssFile`, and `DocOut`

If ComponentFile/CssFile missing:
- ErrorCode: `WF_DEPENDENCY_MISSING`
- Action: run `PyApp_Component-Code` first.

---

# [3] Documentation Inputs (Read-only)
- Planning file (if present):
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.md`
- Component source file + CSS file
- Description constraints

---

# [4] Documentation Content Rules
Doc must include ONLY what is required for this component.

Hard rule:
- The enterprise 19-section structure is mandatory; keep scope tight by making sections component-specific (no app-wide scope creep).

## Required Sections
1) Overview
2) Paths (component + css)
3) Component Type + UTag (singular/plural + UTag)
4) Purpose / UX intent (from planning/description)
5) Inputs / Data contract (only what component actually uses)
6) Output structure (high-level)
7) Screen/Component/Template runtime behavior (including JS hydration markers when applicable)
8) Dependencies (helpers/engines used, if any)
9) CSS scope + responsive notes
10) Premium UI Checklist (from ACC.UI.Premium)
11) Known Edge Cases (only if observed)
12) Changelog

## Forbidden
- App-wide docs
- Template/section docs
- Extra components listing
- Extra suggestions not requested

---

# [5] Save Strategy
- If DocOut exists: update in-place (keep older content)
- Maintain a changelog:
  - Append a new entry per run
- Do not delete older docs

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_DEPENDENCY_MISSING
- WF_PERMISSION_DENIED
