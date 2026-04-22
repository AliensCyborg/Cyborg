---
WorkflowId: WebApp_Screen
Type: Workflow_Plural
Domain: WebApp
Scope: Generate WebApp Screens (planning + code + docs) via child workflows
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
    - WorkflowId: "WebApp_Screen"
    - Targets: "App/Screen, App/Screen, ..."
    - Description: "Human constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Explicit outputs only"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **1 ya multiple WebApp Screens** generate karwata hai.
Is file ka kaam sirf orchestration hai: har Target par:
1) Planning
2) Code
3) Documentation

Example:
- Workflow( "WebApp_Screen", "AlienCyborg/Deposit, AlienCyborg/Home", "UI premium hona zarori hai" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName/ScreenName`
- Comma-separated list = multiple runs

## 2.2 Normalization (Deterministic)
- Trim spaces around commas
- Preserve case
- If any target is empty => error `WF_TARGET_INVALID`

## 2.3 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

Normalize Screen basename:
- If `ScreenName` starts with `{AppName}.` => keep
- Else if `ScreenName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{AppName}.{ScreenName}`

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/ScreenName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/WebApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
  - ScreenNameNormalized: `{AppName}.{ScreenName}` (unless already prefixed)
  - ScreenOut: `{AppRoot}/User/screen/{ScreenNameNormalized}.php`
  - CssOut: `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
Call:
- Workflow( "WebApp_Screen-Planning", "{AppName}/{ScreenName}", "{Description}" )

## Step B1: Section Dependency Resolution (Mandatory)
Rule (Screen = composition only):
- Screen ke andar koi naya section/design generate nahi hoga.
- Screen sirf existing Sections ko **call** karega.

Dependency policy:
- Planning se `RequiredSections[]` nikalo.
- For each required section:
  - Normalize name via App prefix rule (`{AppName}.`), avoid double-prefix.
  - Check existence in same theme:
    - `{AppRoot}/User/section/{SectionNameNormalized}.php`
    - `{AppRoot}/User/css/{SectionNameNormalized}-Section.css` (canonical)
    - `{AppRoot}/User/css/{SectionNameNormalized}.css` (legacy)
  - If exists: DO NOT create, sirf call.
  - If missing: pehle `WebApp_Section` workflow run karo (loop / per section), phir screen me call.

Call format (strict):
- `Section( 'Section_Name', Data_Array, return=true|false )`

If any required section cannot be resolved/created:
- ErrorCode: `WF_SECTION_DEPENDENCY_FAILED`
- Action: run `WebApp_Section` for missing sections, then re-run screen.

## Step C: Code Generation (Child Workflow)
Call:
- Workflow( "WebApp_Screen-Code", "{AppName}/{ScreenName}", "{Description}" )

## Step C.5: Reaudit (Post-Code Verification, Read-only)
- Workflow( "WebApp_Screen-Reaudit", "{AppName}/{ScreenName}", "{Description}" )

Loop Rule (Non-Negotiable):
- `compliant`    => proceed to Documentation.
- `noncompliant` => loop back to Planning -> Code -> Reaudit until compliant.
- Documentation MUST NOT run while verdict is `noncompliant`.
- See `_Common/Workflow_Plural.md` [05A].

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "WebApp_Screen-Documentation", "{AppName}/{ScreenName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of created/updated files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/WebApp/...`
- If manifest says `Approval=auto`: proceed.

---

# [5] Hard Boundaries (No Extra Work)
- Scope **sirf**: given targets ke screens + optional screen CSS + docs.
- Screen ke andar section/design generate mat karna.
- Allowed dependency creation (no extra pause/approval prompts):
  - Missing Sections ko `WebApp_Section` workflow se auto-create karna (same theme), phir screen me call karna.
  - If those Sections Planning contract defines `RequiredComponents[]`, then `WebApp_Section` dependency resolver may auto-run `WebApp_Component` to create missing components (same theme) — **without stopping for separate approval**.
  - Note: yeh “no approval” ka matlab hai “no extra user prompt/pause”; manifest `Approval` gating rules still apply for writing Code/Docs.
- App-level routing/menu/register changes **mat** karna.

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED
