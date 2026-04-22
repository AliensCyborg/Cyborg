---
WorkflowId: PyApp_Component_Update-Code
Type: Workflow_Singular-Code
Domain: PyApp
Scope: Update a PyApp Component + its CSS file (modify-only)
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
    - WorkflowId: "PyApp_Component_Update-Code"
    - Targets: "App/Component"
    - Description: "Update constraints only (no scope expansion)"
Outputs:
  - ComponentFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/component/{ComponentNameNormalized}.py"
  - CssFile: "/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/User/css/{ComponentNameNormalized}.css"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Modify-only: update existing files only"
  - "Missing target is error"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Products.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Products.css"
...

# [0] Purpose
Given `AppName/ComponentName`, yeh workflow existing component ko **modify-only update** karta hai:
1) Component Python file
2) Component CSS file (`.css`)

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`
- ComponentNameNormalized (canonical; SSOT): prefix rule per `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

Backward-compatible update lookup (NON-NEGOTIABLE: no rename/move):
- Define `{ComponentNameLegacyUnderscore}` as the legacy basename variant:
  - If input already starts with `{AppName}_` => keep as-is
  - Else if input starts with `{AppName}.` => convert ONLY the first separator to underscore:
    - `AppName.Rest` => `AppName_Rest`
  - Else => `{AppName}_{ComponentName}`

- ComponentFileCandidates (pick first that exists):
  - `{AppRoot}/User/component/{ComponentNameNormalized}.py`
  - `{AppRoot}/User/component/{ComponentNameLegacyUnderscore}.py`

- CssFileCandidates (pick first that exists):
  - `{AppRoot}/User/css/{ComponentNameNormalized}.css`
  - `{AppRoot}/User/css/{ComponentNameLegacyUnderscore}.css`

- ComponentOut: first existing from ComponentFileCandidates
- CssOut: first existing from CssFileCandidates

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

Update-only precondition:
- If ComponentOut missing => `WF_COMPONENT_NOT_FOUND`
- If CssOut missing => `WF_COMPONENT_CSS_NOT_FOUND`

---

# [3] Planning Dependency (Hard Gate)
Load update planning file:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.md`

Deterministic audit artifact (hard gate):
- `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.Update.audit.md`

If audit not found:
- ErrorCode: `WF_AUDIT_REQUIRED`
- Action: run `PyApp_Component_Update-Audit`

If planning not found:
- ErrorCode: `WF_PLANNING_REQUIRED`
- Action: run `PyApp_Component_Update-Planning`

---

# [4] Update Rules
## 4.1 Component Philosophy (Preserve Theme)
- Component should stay UI-focused (no business logic).
- Follow AliensStyle header/footer.
- Prefer safe echo/heredoc output; avoid risky inline Python islands in markup.

Hard rules (same as create workflow):
- Component files MUST NOT declare any Python functions (`function X(){}`), anonymous functions/closures, or classes.
- Markup/template must use ONLY `$Data[...]` (no locals like `$meta/$state/$config/$otpLength`).
- Any condition/calculation/normalization/escaping/merging/Component() calls MUST be inside BackendGuard.

Template control-flow rule (hard):
- No `if/foreach/for` inside markup/template region.
- Any conditional rendering must be resolved inside BackendGuard by preparing `$Data['...Html']` fragments.
- Template should only `echo $Data[...]`.

## 4.2 Modify-only Contract (Non-negotiable)
- No file delete.
- No rename/move.
- No overwrite from scratch.
- Preserve existing semantics unless planning explicitly changes it.

## 4.3 Allowed Change Categories
Only apply changes that appear in update planning:
- UI/layout refinements inside component markup
- Data contract adjustments (only what is required)
- CSS scope + responsive improvements inside the resolved component CSS file (`CssOut`)

Forbidden unless Description explicitly requests:
- Creating new components/sections/screens/templates
- App-level routing/menu/register changes

## 4.4 CSS Rules (Update)
- Preserve existing component CSS filename (no rename/move). SSOT prefers `AppName.<Basename>.css` for new work.
- CSS MUST remain scoped to the component UTag selectors.
- Use theme variables only (e.g. `--b, --c, --bt, --bc`).
- Selector depth (spaces) strict:
  - Best: `utag .a1`
  - Max: `selector1 selector2 selector3`
  - If more depth needed: add a short class and target it
- Do NOT depend on `.{{AppName}}_*` classes in selectors (remove/replace in update if present).
- Markup classes must be extra-short (`a1..a9`, `b1..b9`).
- CSS formatting (strict): one selector per line `selector{ prop:val; prop:val; }` and no blank lines.

## 4.5 BackendGuard Rule (Mandatory)
Component Python me heavy processing (conditions/loops/array_merge/htmlspecialchars/component calls) **sirf** is guard ke andar:

`global $Screen; if ( defined('Screen') && Screen && is_array($Array) && ( !empty($Array) || !empty($Screen) ) ) { ... }`

Guard ke bahar:
- near-zero Python
- `$Array` merge/loop/formatting **mat** karo

Front preview rule:
- Preview/default UI ke liye caller simply `Component('X')` (no args) call kar sakta hai.
- Update workflow me by-default hardcoded demo/static dataset add karna **forbidden**.

Dummy dataset policy (update):
- Default hardcoded demo dataset **mat** add karo.
- Dummy/default dataset handling sirf guard ke andar.
- Explicit dummy: caller `$Array['dummy']=1`.
- Default dataset: jab component no payload ke sath Screen render me call ho (PyOS `Component()` global `$Screen=true` set karta hai).

Plural reminder:
- Plural component me loop + `Component('App_Singular', $Item, true)` **sirf** guard ke andar.
- Guard ke bahar `Component()` calls **mat** karo.

Component type rules (non-negotiable; align with SSOT):
- **Singular Component**: kabhi bhi kisi bhi type ka `Component()` call nahi karega.
- **Plural Component**: sirf per-item rendering ke liye **Singular child component** ko call karega (plural -> singular delegation), aur wo bhi BackendGuard ke andar.

Plural + `$Data`-only compatibility (mandatory when both apply):
- If template is `$Data`-only, do NOT call `Component()` directly inside markup.
- Inside BackendGuard, buffer child output and concatenate:
  - `ob_start(); Component('Child', $Item, false); $ItemsHtml .= (string)ob_get_clean();`
  - `$Data['ItemsHtml'] = $ItemsHtml;`
- Then in template render only: `echo $Data['ItemsHtml'];`

---

# [5] Save Outputs
- Patch ComponentOut + CssOut (modify-only)
- Return minimal output summary: updated file paths only

---

# [6] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_COMPONENT_NOT_FOUND
- WF_COMPONENT_CSS_NOT_FOUND
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
