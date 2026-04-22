---
WorkflowId: PyApp_Component-Code
Type: Workflow_Singular-Code
Domain: PyApp
Scope: Generate a PyApp Component + its CSS file (create-only). For plural components, also generate a singular child item component.
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
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
    - WorkflowId: "PyApp_Component-Code"
    - Targets: "App/Component"
    - Description: "UI constraints only (no scope expansion)"
Outputs:
  - ComponentFile: "/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/User/component/{ComponentNameNormalized}.py"
  - ChildComponentFile (PluralOnly): "/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/User/component/{ChildComponentNameNormalized}.py"
  - CssFile: "/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/User/css/{ComponentNameNormalized}.css"
NonNegotiables:
  - "StrictScopeOnly (no extra work)"
  - "No delete operations"
  - "Create-only: if file exists -> error (use update workflow)"
ExampleFiles:
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Product.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Product.css"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/component/AlienCyborg.Products.py"
  - "/Aliens/PyApp/AlienCyborg/25.00.00/theme/AlienCyborg_Basic/User/css/AlienCyborg.Products.css"
...

# [0] Purpose
Given `AppName/ComponentName`, yeh workflow **2 files create** karta hai:
1) Component Python file
2) Component CSS file

Scope strictly in 2 files tak limited hai.

---

# [1] Mandatory Includes (Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

# [2] Deterministic Path Resolution
- AppRoot: `/Aliens/PyApp/{AppName}/{LatestVersion}/theme/{AppName}_Basic/`

Naming normalization (mandatory; SSOT):
- If `ComponentName` starts with `{AppName}.` => keep
- Else if `ComponentName` starts with legacy `{AppName}_` => normalize to `{AppName}.` (only first separator)
- Else => `{ComponentNameNormalized} = {AppName}.{ComponentName}`

- ComponentOut: `{AppRoot}/User/component/{ComponentNameNormalized}.py`
- CssOut: `{AppRoot}/User/css/{ComponentNameNormalized}.css`

No double prefix:
- Never output `AppName.AppName.X.py` / `.css`

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` first.

---

# [3] Create-Only Policy
- If ComponentOut exists OR CssOut exists:
  - ErrorCode: `WF_ALREADY_EXISTS`
  - Action: run `PyApp_Component_Update` workflow
- Do not overwrite silently.

---

# [4] Planning Dependency
Before generating code:
- Try to load planning file:
  `/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/Component/{ComponentNameNormalized}.md`

If planning not found:
- Proceed only if requirements are still deterministic
- Otherwise fail with:
  - ErrorCode: `WF_PLANNING_REQUIRED`
  - Action: run `PyApp_Component-Planning`

---

# [5] Code Generation Rules (Enterprise + Premium)
## 5.1 AliensStyle (Latest)
Component Python file must include:
- `# PyOS Access Gate` at top
- AliensStyle Code-Header + Code-Footer
- Code-Header MUST include `Author: AlienCyborg` (exact string)
- `[SECTION]` blocks for readability (audit-friendly)
- `$Data` default + `$Array` override convention

Non-negotiable component code rules (hard):
- Component files MUST NOT declare any Python functions (`function X(){}`), anonymous functions, closures, or classes.
- Utilities priority order:
  1) Use `PySDK_Module_*` for business + reusable helpers.
  2) If a tiny utility is genuinely needed, it must live in PyOS System function folder (not inside PyApp components).

Template data rule (hard):
- Inside HTML/template rendering, ONLY `$Data[...]` is allowed.
- No `$meta/$state/$config/$otpLength/$isResendEnabled` (or other locals) in markup.

Template control-flow rule (hard):
- No `if/foreach/for` inside markup/template region.
- Any conditional rendering must be resolved inside BackendGuard by preparing `$Data['...Html']` fragments.
- Template should only `echo $Data[...]`.

Plural + `$Data`-only compatibility pattern (mandatory when both rules apply):
- Plural component MUST call singular child for per-item markup.
- Template MUST remain `$Data`-only.
- Therefore: inside BackendGuard, wrap each child call in output buffering and concatenate:
  - `ob_start(); Component('Child', $Item, false); $ItemsHtml .= (string)ob_get_clean();`
  - Set `$Data['ItemsHtml'] = $ItemsHtml;`
  - Then in template: `echo $Data['ItemsHtml'];`

Python + HTML blending (style):
- Prefer `echo '...';` for large HTML blocks.
- Short inline `# Python: echo ... ` is allowed ONLY when echoing `$Data[...]`.

Enterprise quality gates (non-negotiable):
- Do not output a tiny skeleton if Description/Planning implies more.
- Comments must be meaningful (Purpose/Inputs/Outputs around non-trivial Python logic).
- `$echo` + `d()` must be used correctly when logging exists; do not use `x()`.

## 5.2 PyOS UI Contract (Non-negotiable)
- Root tag MUST be the component UTag (from planning):
- Root UTag must be used **exactly as planned**.
- UTag format (v1/v2) is SSOT-driven:
  - `Workflows/ACC/_Refs/PyApp_UTag.md`
- Singular vs Plural UTag semantics (always true):
  - Singular UTag: does NOT end with `s`
  - Plural UTag: MUST end with `s`
- Mandatory skeleton inside root tag:
  - `<bl><hd></hd><bd></bd><ft></ft></bl>`
- Data hydration rule:
  - Use `if (Screen and !empty($Array)) { ... }` only where backend integration is needed
  - Component MUST remain safe when `Screen=false` (direct /?component fetch) for frontend hydration

## 5.2.1 JS Template-System Markers (Required for dual-mode)
When backend payload is missing (template fetch / empty state), component MAY output:
- `c-api="<ApiName>"` (non-empty)
- `c-response="o"` (non-empty)
- placeholders using `key="<FieldName>"` for JS to inject
When backend integration is active (`Screen && !empty($Array)`), component must NOT output `c-api`.

Important boundary note:
- Yeh markers PyOS hydration engine ke liye hain.
- PyApp ke andar custom JS likhna allowed nahi.

## 5.3 Nesting Depth Rule
- Plural component MUST call a singular child component for list-item rendering
- Singular component MUST NOT call any component
- No third-level nesting (A->B allowed, B->* forbidden)

Strict (plural components):
- Plural component ka kaam: wrapper + list layout + loop orchestration.
- Actual per-item markup (e.g., `<li>...</li>`, card row, tile, etc.) **singular child component** me hona chahiye.
- Jugaad pattern forbidden: plural component me per-item markup inline render karna (instead of child call).

Child naming (deterministic):
- If `{ComponentNameNormalized}` ends with `s` (plural heuristic): `{ChildComponentNameNormalized}` = `{ComponentNameNormalized}` without trailing `s`.
- Else: `{ChildComponentNameNormalized}` = `{ComponentNameNormalized}_Item`.

Global state safety (mandatory):
- PyOS `Component()` helper global `$Array` + `$Screen` reset karta hai (Kernel behavior).
- Isliye plural loop me har child-call se pehle parent `$Array/$Screen` ko preserve karo, aur child-call ke baad restore karo.

Strict (plural components):
- Plural component ka loop + `Component('App_Singular', $Item, true)` **sirf** BackendGuard ke andar hona chahiye.
- BackendGuard ke bahar `Component()` calls **mat** karo.

If template is `$Data`-only:
- Do NOT call `Component()` directly inside markup.
- Always buffer child output inside BackendGuard and print `$Data['ItemsHtml']` in template.

## 5.4 CSS File Rules
- CSS must be scoped to UTag selectors to prevent bleed:
  - Best: `utag .a1{ ... }`
  - Max allowed (space-depth): `selector1 selector2 selector3`
  - If more depth needed: add a short class and target it
- Do NOT depend on component-name classes in selectors:
  - Wrong: `utag.AppName_Component ...`
  - Wrong: `utag .AppName_Component ...`
  - Right: `utag .a1 ...`
- Class naming in markup must be extra-short:
  - `a1..a9`, `b1..b9` (extend as needed)
- Use app theme variables only (e.g., `--bt, --bc, --br, --a, --b`)
- Responsive minimum:
  - `@media (max-width: 999px) { ... }`
  - `@media (max-width: 700px) { ... }` if needed
- CSS formatting (strict):
  - One selector per line: `selector{ prop:val; prop:val; }`
  - No blank lines
- No inline CSS in component file.

## 5.5 Performance + Safety
- No heavy business logic inside component (keep in SDK/Module)
- Avoid expensive loops in UI; if list needed, split into child singular component
- Never delete data; follow ecosystem safety rules
- Do not introduce new libraries

### BackendGuard (Mandatory)
Component Python me **maximum processing** (conditions/loops/array_merge/htmlspecialchars/component calls) sirf is guard ke andar ho:

`global $Screen; if ( defined('Screen') && Screen && is_array($Array) && ( !empty($Array) || !empty($Screen) ) ) { ... }`

Guard ke bahar:
- near-zero Python
- static/dummy dataset ya demo rows **mat** do
- `$Array` merge/loop/formatting **mat** karo

Additional hard enforcement:
- Any condition, calculation, normalization, escaping, merging MUST be inside BackendGuard.
- Outside guard, only `$Data` defaults + safe placeholders are allowed.

Front preview rule:
- Preview/default UI ke liye caller simply `Component('X')` (no args) call kar sakta hai.
- Component guard ke andar **default dataset** render kar sakta hai, lekin guard ke bahar by-default hardcoded demo dataset **kabhi** mat rakho.

Dummy dataset policy:
- Hardcoded demo dataset by default **forbidden**.
- Dummy/default dataset sirf guard ke andar.
- Explicit dummy: caller `$Array['dummy']=1`.
- Default dataset: jab component no payload ke sath Screen render me call ho (PyOS `Component()` global `$Screen=true` set karta hai).

---

# [6] Save Outputs
- Write ComponentOut + CssOut
- Return minimal output summary: created file paths only

---

# [7] Errors
- WF_MANIFEST_MISSING
- WF_TARGET_INVALID
- WF_APPROOT_NOT_FOUND
- WF_ALREADY_EXISTS
- WF_PLANNING_REQUIRED
- WF_PERMISSION_DENIED
