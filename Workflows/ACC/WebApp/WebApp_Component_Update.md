---
WorkflowId: WebApp_Component_Update
Type: Workflow_Plural
Domain: WebApp
Scope: Update existing WebApp Components (planning + code + docs) via child workflows (modify-only)
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
    - WorkflowId: "WebApp_Component_Update"
    - Targets: "App/Component, App/Component, ..."
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - "Delegated to child workflows (Planning + Code + Documentation)"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Update-only: missing targets => error"
  - "Modify-only: update existing files only"
  - "Fail fast on ambiguity"
...

# [0] Purpose
Yeh workflow **existing WebApp Components** ko update karwata hai.
Is file ka kaam orchestration hai: per Target:
0) Audit (read-only)
1) Planning (update plan)
2) Code (modify-only update)
3) Documentation (create-or-update docs)

Example:
- Workflow( "WebApp_Component_Update", "AlienCyborg/DepositNew, Social/Post", "UI refine + inputs update" )

---

# [1] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Target Parsing Rules
## 2.1 Accepted Target Format
- `AppName/ComponentName`
- Comma-separated list = multiple runs

## 2.2 Normalization (Deterministic)
- Trim spaces around commas
- Preserve case
- If any target is empty => error `WF_TARGET_INVALID`

## 2.3 App Prefix Rule (Mandatory)
Naming SSOT:
- `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

Normalize Component basename:
- If `ComponentName` starts with `{AppName}.` => keep
- Else if `ComponentName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{AppName}.{ComponentName}`

---

# [3] Primary Execution Steps (Per Target)
For each target `AppName/ComponentName`:

## Step A: Validate + Resolve
- Read manifest: `/Aliens/manifest.json`
- Validate approval + permissions
- Resolve paths deterministically:
  - AppRoot: `/Aliens/WebApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`
  - ComponentNameNormalized: canonical (SSOT)
  - Update-only lookup must resolve the *existing* files (dot or legacy underscore) without rename/move.
  - ComponentOut: resolved existing component file
  - CssOut: resolved existing css file

Update-only resolution rule:
- ComponentOut missing => error `WF_COMPONENT_NOT_FOUND`
- CssOut missing => error `WF_COMPONENT_CSS_NOT_FOUND`

If AppRoot/ThemeRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `WebApp_Structure` workflow first
- Do not auto-create app/theme in this workflow

## Step B: Planning (Child Workflow)
## Step A1: Audit (Child Workflow)
Call:
- Workflow( "WebApp_Component_Update-Audit", "{AppName}/{ComponentName}", "{Description}" )

## Step B: Planning (Child Workflow)
Call:
- Workflow( "WebApp_Component_Update-Planning", "{AppName}/{ComponentName}", "{Description}" )

## Step C: Code Update (Child Workflow)
Call:
- Workflow( "WebApp_Component_Update-Code", "{AppName}/{ComponentName}", "{Description}" )

## Step C.5: Reaudit (Post-Code Verification, Read-only)
- Workflow( "WebApp_Component_Update-Reaudit", "{AppName}/{ComponentName}", "{Description}" )

Loop Rule (Non-Negotiable):
- `compliant`    => proceed to Documentation.
- `noncompliant` => loop back to Planning -> Code -> Reaudit until compliant.
- Documentation MUST NOT run while verdict is `noncompliant`.
- See `_Common/Workflow_Plural.md` [05A].

## Step D: Documentation (Child Workflow)
Call:
- Workflow( "WebApp_Component_Update-Documentation", "{AppName}/{ComponentName}", "{Description}" )

## Step E: Result Summary (Minimal)
- Return only list of updated files (no extra suggestions)

---

# [4] Approval Gating (Intent Only)
- If manifest says `Approval=manual`:
  - Planning output can be generated without approval
  - Code + Docs steps must be approval-gated when writing to `/Aliens/WebApp/...`
- If manifest says `Approval=auto`: proceed.

---

# [5] Hard Boundaries (No Extra Work)
- Scope sirf: given targets ke existing component + its CSS + docs.
- New components auto-create mat karna.
- New sections/screens/templates auto-create mat karna.
- App-level routing/menu/register changes mat karna.

Performance rule:
- Component PHP updates me BackendGuard discipline enforce karna zarori hai:
  - heavy work only inside `global $Screen; if ( defined('Screen') && Screen && is_array($Array) && ( !empty($Array) || !empty($Screen) ) ) { ... }`
  - outside: near-zero PHP (static HTML ok)

Hard rules (must be enforced in update code):
- Component files MUST NOT declare any PHP functions/classes/closures.
- Any condition/calculation/normalization/escaping/merging/Component() calls MUST be inside BackendGuard.
- Markup/template must use ONLY `$Data[...]` (no locals like `$meta/$state/$config`).
- Plural components MUST still render per-item markup via singular child; if template is `$Data`-only, use output buffering inside guard and print `$Data['ItemsHtml']`.

---

# [6] Standard Errors
Use universal error model:
- ErrorCode, Message, Details, Action

Expected errors:
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_COMPONENT_NOT_FOUND
- WF_COMPONENT_CSS_NOT_FOUND
- WF_PERMISSION_DENIED
- WF_APPROVAL_REQUIRED

---

# [7] CSV Mode (Optional)
If workflow is invoked via Tasks CSV:
- Process exactly ONE pending row (`status=0`) per run.
- If more than one pending row exists, hard-fail with `WF_CSV_BATCH_FORBIDDEN`.
