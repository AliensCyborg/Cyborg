---
WorkflowId: PyApp_Section_Update-Documentation
Type: Workflow_Singular-Documentation
Domain: PyApp
Scope: Update documentation for an existing PyApp Section
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
    - WorkflowId: "PyApp_Section_Update-Documentation"
    - Targets: "App/Screen_Section"
    - Description: "Doc update constraints (no scope expansion)"
Outputs:
  - DocFile: "/Aliens/Docs/PyApp/{AppName}/Sections/{SectionNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Missing target is error"
...

# [0] Purpose
Given `AppName/SectionName`, yeh workflow existing section ki **documentation update** karta hai.

# [0A] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if planning/audit/code info missing
- Security/Performance/Observability/Testing sections required

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- SectionNameNormalized: prefix rule per SSOT (and must contain underscore)
- SectionFile: `{AppRoot}/User/section/{SectionNameNormalized}.py`
- CssFileCandidates (pick first that exists; update-only compatibility; do not rename/move):
  - `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` (SSOT / canonical)
  - `{AppRoot}/User/css/{SectionNameNormalized}.css` (legacy transitional)
  - `{AppRoot}/User/css/{SectionNameLegacyUnderscore}-Section.css` (legacy)
  - `{AppRoot}/User/css/{SectionNameLegacyUnderscore}.css` (rare transitional)
- CssFile: first existing from CssFileCandidates
- DocOut: `/Aliens/Docs/PyApp/{AppName}/Sections/{SectionNameNormalized}.md`

If SectionFile/CssFile missing:
- ErrorCode: `WF_DEPENDENCY_MISSING`
- Action: run `PyApp_Section_Update-Code` (or create workflow if missing)

---

# [3] Documentation Inputs (Read-only)
- Update planning file (required):
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Section/{SectionNameNormalized}.Update.md`
- Deterministic audit artifact (required):
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Section/{SectionNameNormalized}.Update.audit.md`
- Section source file + CSS file
- Description constraints

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `PyApp_Section_Update-Planning`

If audit not found:
- ErrorCode: `WF_AUDIT_REQUIRED`
- Action: run `PyApp_Section_Update-Audit`

---

# [4] Documentation Content Rules
Doc must include ONLY what is required for this section.

Hard rule:
- The enterprise 19-section structure is mandatory; keep scope tight by making sections section-specific (no app-wide scope creep).

Required sections:
1) Overview
2) Paths (section + css)
3) Purpose / UX intent (from planning/description)
4) Components used (if any)
5) Screen guard behavior (if any)
6) CSS scope + responsive notes
7) Premium UI Checklist
8) Changelog

Forbidden:
- App-wide docs
- Listing unrelated sections/components
- Scope expansion

---

# [5] Save Strategy
- If DocOut exists: update in-place (keep older content)
- Append a changelog entry per run
- Do not delete older docs

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_DEPENDENCY_MISSING
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
