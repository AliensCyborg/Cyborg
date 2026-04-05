---
WorkflowId: WebApp_Component_Update-Documentation
Type: Workflow_Singular-Documentation
Domain: WebApp
Scope: Update documentation for an existing WebApp Component
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
  - Workflows/ACC/_Refs/WebOS_Engines.md
  - Workflows/ACC/_Refs/WebOS_System.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Component_Update-Documentation"
    - Targets: "App/Component"
    - Description: "Doc update constraints (no scope expansion)"
Outputs:
  - DocFile: "/Aliens/Docs/WebApp/{AppName}/Components/{ComponentNameNormalized}.md"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Missing target is error"
...

# [0] Purpose
Given `AppName/ComponentName`, yeh workflow existing component ki **documentation update** karta hai.

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
- AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ComponentNameNormalized: prefix rule per SSOT

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{ComponentNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{ComponentName}`

- ComponentFileCandidates (pick first that exists):
  - `{AppRoot}/User/component/{ComponentNameNormalized}.php`
  - `{AppRoot}/User/component/{ComponentNameLegacyUnderscore}.php`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{ComponentNameNormalized}.css`
  - `{AppRoot}/User/css/{ComponentNameLegacyUnderscore}.css`

- ComponentFile: first existing from ComponentFileCandidates
- CssFile: first existing from CssFileCandidates
- DocOut: `/Aliens/Docs/WebApp/{AppName}/Components/{ComponentNameNormalized}.md`

If ComponentFile/CssFile missing:
- ErrorCode: `WF_DEPENDENCY_MISSING`
- Action: run `WebApp_Component_Update-Code` (or create workflow if missing)

---

# [3] Documentation Inputs (Read-only)
- Update planning file (required):
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Component/{ComponentNameNormalized}.Update.md`
- Deterministic audit artifact (required):
  `/Aliens/.Alien/{Developer_Username}/Planning/WebApp/{AppName}/Component/{ComponentNameNormalized}.Update.audit.md`
- Component source file + CSS file
- Description constraints

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `WebApp_Component_Update-Planning`

If audit not found:
- ErrorCode: `WF_AUDIT_REQUIRED`
- Action: run `WebApp_Component_Update-Audit`

---

# [4] Documentation Content Rules
Doc must include ONLY what is required for this component.

Hard rule:
- The enterprise 19-section structure is mandatory; keep scope tight by making sections component-specific (no app-wide scope creep).

Required sections:
1) Overview
2) Paths (component + css)
3) Component Type + UTag (as observed)
4) Purpose / UX intent (from planning/description)
5) Inputs / Data contract (only what component actually uses)
6) Output structure (high-level)
7) CSS scope + responsive notes
8) Premium UI Checklist (from ACC.UI.Premium)
9) Changelog

Implementation constraints (must be documented when relevant to the update):
- No functions/classes/closures inside component file
- BackendGuard discipline: all processing inside guard; outside near-zero PHP
- Template renders only `$Data[...]` (or `$Data['ItemsHtml']` prepared in guard)
- For plural components: singular child renders per-item markup; buffered output may be used for `$Data`-only templates

Forbidden:
- App-wide docs
- Listing unrelated components
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
- WF_APPROOT_NOT_FOUND
- WF_DEPENDENCY_MISSING
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
