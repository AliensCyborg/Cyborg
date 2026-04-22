---
WorkflowId: WebApp_Section
Type: Workflow_Plural
Domain: WebApp
Scope: Generate WebApp Sections (planning + code + docs) via child workflows
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Section"
    - Targets: "App/Screen_Section, App/Screen_Section, ..."
    - Description: "Human constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Create-only: if file exists -> error (use update workflow)"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **1 ya multiple WebApp Sections** generate karwata hai.
Is file ka kaam orchestration hai: har Target par:
1) Planning
2) Code
3) Documentation

Example:
- Workflow( "WebApp_Section", "AlienCyborg/Deposit_Header, AlienCyborg/Home_Hero", "Simple clean UI" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format (ACC.Targets compatible)
- `AppName/SectionName`
- Comma-separated list = multiple runs

Important:
- ACC.Targets default policy deep paths reject karta hai.
- Isliye Section target me **sirf ek slash** allowed hai.

## 2.2 SectionName Meaning (Deterministic)
`SectionName` must include ScreenName + SectionPart separated by underscore:
- `ScreenName_SectionName`
Examples:
- `Deposit_Header`
- `Home_Hero`

If user passes only `Header` (no underscore):
- Error `WF_SECTION_NAME_AMBIGUOUS`

## 2.3 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

Normalize Section basename:
- If `SectionName` already starts with `{AppName}.` => keep
- Else => `{AppName}.{SectionName}`

Important:
- Do not double-prefix.

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/SectionName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
  - SectionNameNormalized: `{AppName}.{SectionName}` (unless already prefixed)
  - SectionOut: `{AppRoot}/User/section/{SectionNameNormalized}.php`
    - CssOut: `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`

Create-only rule:
- If SectionOut exists OR CssOut exists => error `WF_ALREADY_EXISTS`
- Legacy safety check (do not rename/move):
  - If `{AppRoot}/User/css/{SectionNameNormalized}.css` exists => treat as exists, use update workflow
- Action: run `WebApp_Section_Update` (not part of this request)

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
Call:
- Workflow( "WebApp_Section-Planning", "{AppName}/{SectionName}", "{Description}" )

## Step B1: Component Dependency Resolution (Mandatory)
Rule (Section = composition only):
- Section ke andar koi naya component/design generate nahi hoga.
- Section sirf existing Components ko **call** karega.

Layout-only clarification (STRICT):
- Section file me feature-level UI/design blocks (e.g. Filters checkboxes, Product cards, Pagination buttons) directly markup me **mat** banao.
- Section me sirf choti-moti layout skeleton allowed hai (e.g. `aerea/sidebar/main/grid` wrappers).
- Actual reusable UI hamesha Components me banao, aur Section se `Component('App_Component')` calls karo.

Dependency policy:
- Planning se `RequiredComponents[]` nikalo.
- For each required component:
  - Normalize name via App prefix rule (`{AppName}_`), avoid double-prefix.
  - Check existence in same theme:
    - `{AppRoot}/User/component/{ComponentNameNormalized}.php`
    - `{AppRoot}/User/css/{ComponentNameNormalized}.css`
  - If exists: DO NOT create, sirf call.
  - If missing: pehle `WebApp_Component` workflow run karo (loop / per component), phir call.

No extra approval rule:
- Dependency creation me **additional user approval/pause** nahi hoga.
- Meaning: jab Section ko component chahiye aur missing ho, runner `WebApp_Component` ko auto-run kar sakta hai.
- Note: manifest `Approval` gating rules still apply for writing Code/Docs.

Call format (strict):
- `Component( 'Component_Name', Data_Array, return=true|false )`

If any required component cannot be resolved/created:
- ErrorCode: `WF_COMPONENT_DEPENDENCY_FAILED`
- Action: run `WebApp_Component` for missing components, then re-run section.

## Step C: Code Generation (Child Workflow)
Call:
- Workflow( "WebApp_Section-Code", "{AppName}/{SectionName}", "{Description}" )

## Step C.5: Reaudit (Post-Code Verification, Read-only)
- Workflow( "WebApp_Section-Reaudit", "{AppName}/{SectionName}", "{Description}" )

Loop Rule (Non-Negotiable):
- `compliant`    => proceed to Documentation.
- `noncompliant` => loop back to Planning -> Code -> Reaudit until compliant.
- Documentation MUST NOT run while verdict is `noncompliant`.
- See `_Common/Workflow_Plural.md` [05A].

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "WebApp_Section-Documentation", "{AppName}/{SectionName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of created files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/WebApp/...`
- If manifest says `Approval=auto`: proceed.

---

# [5] Hard Boundaries (No Extra Work)
- Scope sirf: given targets ke section + its CSS + docs.
- Screen/template wiring mat karna.
- Section ke andar component/design generate mat karna.
- Allowed dependency creation: sirf missing components ko `WebApp_Component` workflow se create karna (same theme), phir section me call karna.

Note:
- CSS selector + formatting SSOT: `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_SECTION_NAME_AMBIGUOUS
- WF_APPROOT_NOT_FOUND
- WF_ALREADY_EXISTS
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED
