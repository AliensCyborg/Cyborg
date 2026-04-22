---
WorkflowId: PyApp_Screen-Documentation
Type: Workflow_Singular-Documentation
Domain: PyApp
Scope: Generate documentation for a PyApp Screen
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
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
    - WorkflowId: "PyApp_Screen-Documentation"
    - Targets: "App/Screen"
    - Description: "Doc constraints (no scope expansion)"
Outputs:
  - DocFile: "/Aliens/Docs/PyApp/{AppName}/Screens/{ScreenNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
...

# [0] Purpose
Given `AppName/ScreenName`, yeh workflow Screen ki **documentation** generate karta hai taaki:
- composition (sections) clear ho
- naming rules consistent rahein
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
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ScreenFile: `{AppRoot}/User/screen/{ScreenNameNormalized}.py`
- CssFileCandidates (pick first that exists; no rename/move):
  - `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css` (SSOT / canonical)
  - `{AppRoot}/User/css/{ScreenNameNormalized}.css` (legacy transitional)
- CssFile: first existing from CssFileCandidates
- DocOut: `/Aliens/Docs/PyApp/{AppName}/Screens/{ScreenNameNormalized}.md`

If ScreenFile/CssFile missing:
- ErrorCode: `WF_DEPENDENCY_MISSING`
- Action: run `PyApp_Screen-Code` first.

---

# [3] Documentation Inputs (Read-only)
- Planning file:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.md`
- Screen source file + Screen CSS file
- Description constraints

---

# [4] Documentation Content Rules
Doc must include ONLY what is required for this screen.

## Required Sections
1) Overview
2) Paths (screen + css)
3) Naming normalization (raw vs normalized)
4) Screen root tag + optional scope tag
5) Sections list + order
6) Runtime behavior (Screen/Section role)
7) CSS scope + responsive notes
8) Premium checklist (minimal)
9) Changelog

## Forbidden
- App-wide docs
- Section implementation docs (only list them)
- Extra suggestions not requested

---

# [5] Save Strategy
- If DocOut exists: update in-place
- Maintain a changelog (append per run)
- Do not delete older docs

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_DEPENDENCY_MISSING
- WF_PERMISSION_DENIED
