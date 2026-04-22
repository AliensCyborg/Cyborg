---
WorkflowId: PyApp_Screen_Update-Code
Type: Workflow_Singular-Code
Domain: PyApp
Scope: Update a PyApp Screen + its Screen CSS file (modify-only)
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-30
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.UI.Premium.md
  - Workflows/ACC/_Refs/PyOS_Engines.md
  - Workflows/ACC/_Refs/PyOS_System.md
  - Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Screen_Update-Code"
    - Targets: "App/Screen"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - ScreenFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/screen/{ScreenNameNormalized}.py"
  - CssFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/css/{ScreenNameNormalized}-Screen.css"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Modify-only: update existing files only"
  - "Missing target is error"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposit.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposit-Screen.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/screen/AlienCyborg.Deposits.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Deposits-Screen.css"
...

# [0] Purpose
Given `AppName/ScreenName`, yeh workflow existing screen ko **modify-only update** karta hai:
1) Screen Python file
2) Screen CSS file (preserve existing filename; SSOT prefers matching basename)

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ScreenNameNormalized (canonical; SSOT): prefix rule per `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{ScreenNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{ScreenName}`

- ScreenFileCandidates (pick first that exists):
  - `{AppRoot}/User/screen/{ScreenNameNormalized}.py`
  - `{AppRoot}/User/screen/{ScreenNameLegacyUnderscore}.py`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`         (SSOT / canonical)
  - `{AppRoot}/User/css/{ScreenNameNormalized}.css`                (legacy transitional)
  - `{AppRoot}/User/css/{ScreenNameLegacyUnderscore}-Screen.css`   (legacy)
  - `{AppRoot}/User/css/{ScreenNameLegacyUnderscore}.css`          (legacy)

- ScreenOut: first existing from ScreenFileCandidates
- CssOut: first existing from CssFileCandidates

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

Update-only precondition:
- If ScreenOut missing => `WF_SCREEN_NOT_FOUND`
- If CssOut missing => `WF_SCREEN_CSS_NOT_FOUND`

---

# [3] Planning Dependency (Hard Gate)
Load update planning file:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.md`

Deterministic audit artifact (hard gate):
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Screen/{ScreenNameNormalized}.Update.audit.md`

If audit not found:
- ErrorCode: `WF_AUDIT_REQUIRED`
- Action: run `PyApp_Screen_Update-Audit`

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `PyApp_Screen_Update-Planning`

---

# [4] Update Rules
## 4.1 Screen Philosophy (Preserve Theme)
- Screen is composition-first (Sections are building blocks).
- Screen should not implement business logic.
- Inline Python islands are allowed in Screen files for calling Sections (matches existing theme style).

## 4.1.1 Composition-Only Rule (Non-negotiable)
- Screen ke andar **koi section ya design generate nahi hoga**.
- Screen ka kaam sirf existing sections ko wire/call karna hai.

Hard rule:
- Screen ke andar **Component()** direct call mat karo; Screen sirf `Section(...)` calls karega.
- Section actual me multiple components ko compose karta hai; component actual me multiple standard HTML elements (div/span/button/etc.) se mil kar banta hai.

Meaning:
- Agar update me new section/component chahiye ho, to direct patch se mat banao; dependency workflows use karo (`PyApp_Section`, `PyApp_Component`) and phir Screen update apply karo.

Section call format (strict):
- `Section( 'Section_Name', Data_Array, return=true|false )`

Dependency resolution (allowed, but only via workflow):
- Agar planning me required section missing ho:
  - Direct section code screen file me add mat karna.
  - `PyApp_Section` workflow run karke missing sections create karo (same theme), phir screen update apply karo.

## 4.2 Modify-only Contract (Non-negotiable)
- No file delete.
- No rename/move.
- No silent overwrite from scratch.
- Preserve existing semantics unless Description explicitly changes it.

## 4.3 Allowed Change Categories
Only apply changes that appear in update planning:
- Section order adjustments
- Wrapper tag/scoping improvements
- Small layout refinements (HTML structure inside the screen root/scope)
- CSS scope + responsive fixes inside the resolved Screen CSS file (`CssOut`)

Forbidden unless Description explicitly requests:
- Creating new sections/components inside the screen file
- App-level routing/menu/register changes

## 4.4 CSS Rules (Update)
- Preserve existing Screen CSS filename (no rename/move). SSOT prefers `{ScreenNameNormalized}-Screen.css` for new work.
- Prefer scoping CSS under the screen wrapper/scope tag from planning.
- Use theme variables only (e.g. `--b, --c, --bt, --bc`).

---

# [5] Save Outputs
- Patch ScreenOut + CssOut (modify-only)
- Return minimal output summary: updated file paths only

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_SCREEN_NOT_FOUND
- WF_SCREEN_CSS_NOT_FOUND
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
