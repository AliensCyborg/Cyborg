---
WorkflowId: WebApp-Planner
WorkflowName: WebApp Planner
Type: Workflow_Plural
Domain: WebApp
Scope: Convert planning artifact into executable planner CSV
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp-Planner"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Planner:
	SaveTo: "/Aliens/Project/{Developer_Username}/"
    FileName: "{AppName}_Planner.csv"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/Cyborg/AlienCyborg/Workflows/Examples/Planner/Planner_v1.0.2 .csv"
...

# [0] Purpose
`Workflow("WebApp-Planner", "{AppName}", "{Description}")`

Convert `WebApp-Planning` into an executable CSV planner (task list) similar to the sample `Workflows/Examples/Planner/B-Commerce.csv`.

Additional enterprise reference:
- `Workflows/Examples/Planner/Planner_v1.0.2 .csv` is treated as a **quality benchmark** for `Description` depth (enterprise prompt completeness, security/perf/obs expectations, acceptance gates).
- Canonical CSV schema + safe formatting guidance remains the SSOT in this workflow (do not downgrade schema/quoting rules even if a reference file uses a looser CSV style).

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

# [2] Dependencies (Hard Gate)
Requires planning artifact:
- `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/WebApp-Planning.md`

If missing:
- ErrorCode: `WF_PLANNING_REQUIRED`

---

# [3] Planner CSV Schema (SSOT)
File path (default):
- `/Aliens/Project/{Developer_Username}/{AppName}_Planner.csv`

Header compatibility (NON-NEGOTIABLE):
- Planner CSV header is treated as **case-insensitive** by runners.
- Both variants are valid and MUST be supported:
	- Legacy TitleCase headers (example: `Name`, `Worklflow`, `Planning_Path`)
	- Lowercase headers (example: `name`, `worklflow`, `planning_path`) — used by ultra-large planners.
- When generating a new planner CSV, generator SHOULD prefer the lowercase header variant for max cross-tool compatibility.

Columns MUST match this order (same as sample):
- `ID`
- `Name`
- `utag`
- `Worklflow`
- `Parent`
- `Parent_Type`
- `Description`
- `Prority`
- `Planning`
- `Planning_Save`
- `Code`
- `Code_Save`
- `Docs`
- `Doc_Save`
- `Status`
- `Planning_Path`
- `Code_Path`
- `Doc_Path`
- `Content_Path`
- `DependsOn`
- `Ref`
- `Assign`
- `code_lines`
- `modified`
- `created`

Compatibility aliases (Runner MUST support):
- `Docs` == `doc`
- `Doc_Save` == `doc_save`

Status encoding:
- `0` = pending
- `1` = success
- `2` = failed

Critical rule:
- `Description` column blank nahi ho sakti. Yehi row ka **prompt payload** hai.
- Runner/Development workflow ko isi `Description` ko prompt/context bana ke child workflow ko pass karna hai.

Column notes (SSOT meanings):
- `Worklflow` / `Prority` spellings are **legacy schema** (intentional). Inko “Workflow/Priority” me rename mat karna.
- `utag` = **UTag HTML wrapper tag** (globally unique) for UI artifacts.
	- Scope: **ONLY** for `Screen`, `Section`, `Component` rows.
	- Non-negotiable: WebOS ecosystem me UTag kabhi collide nahi karti (across all WebApp).
	- Allocation SSOT: `Workflows/ACC/_Refs/WebApp_UTag.md`
	- New work MUST use UTag v2 (long tag). Legacy 4/5-letter tags are update-only.
	- Rule:
		- If row `Worklflow` is Screen/Section/Component group (create/update), then `utag` **required** (blank nahi).
		- Otherwise `utag` blank allowed.
	- Note: `ID=0` meta row me blank allowed, but recommended `utag=Meta`.
- `Name` meaning depends on `Worklflow` type:
	- For file-based WebApp UI workflows (`WebApp_Screen`, `WebApp_Section`, `WebApp_Component` + update variants), `Name` is the **filename basename seed** (STRICT).
		- It MUST follow normalization + prefix SSOT in `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`.
		- The AppName prefix rule is non-negotiable: output filenames MUST use the normalized prefixed name.
		- Example good: `B-Commerce_Home`, `B-Commerce_Deposit_Header`
		- Example bad: `Cart Screen`, `Checkout API` (these are titles, not deterministic basenames)
	- For non-file rows (Meta/Docs/general tasks), `Name` can be human-friendly label, but must remain deterministic and short.
- `Parent` + `Parent_Type` = hierarchy link. Example: Parent=`WebApp_Structure`, Parent_Type=`WebApp_Structure`.
- `DependsOn` = dependency row IDs list (comma-separated). Example: `1,2,5`. (Blank allowed.)
	- Meaning: current row MUST NOT execute until all referenced rows are completed successfully.
	- Runner rule (STRICT): before executing `Status=0` row, resolve each dependency ID:
		- If any referenced row ID does not exist => fail fast (schema/dependency gate).
		- If any referenced row has `Status != 1` => fail fast (do not execute current row).
		- Cycles are invalid; dependency graph must be acyclic.
- `Ref` = optional read-only reference pointers to understand requirements. (Blank allowed.)
	- Examples: SSOT doc path, a local file path, or a URL.
	- Runner rule: `Ref` is NOT a replacement for `Description` (prompt payload remains `Description`).
	- If reference is a URL and cannot be fetched (offline), still keep it in CSV + run log; do not guess.
- `Assign` = developer username (task owner) (STRICT).
	- Execution rule: current machine developer MUST match `Assign` for any task with `Status=0`.
	- If mismatch => runner must fail fast (do not execute unassigned task).
	- Example: `Assign=JaneDoe`

Progress tracking (CSV writeback; STRICT when columns exist):
- `code_lines` = is row ke code run me **sirf primary code artifact** (usually `Code_Path`) ke kitne lines create/update hue.
	- Planning/Docs/RunRecord ki lines kabhi include nahi karni.
	- If file created => `code_lines` SHOULD be total lines in that created file.
	- If file updated => `code_lines` SHOULD be diff-based count (added+modified lines; exact method runner implements, but must be deterministic).
	- If `Code=0` => `code_lines=0`.
- `created` = ISO-8601 timestamp jab primary code file create hui (set once; overwrite forbidden).
- `modified` = ISO-8601 timestamp jab primary code file update hui (each update par refresh allowed).

Execution flags:
- `Planning/Code/Docs` are intent flags for what is needed.
- `*_Save` indicates artifact must be saved to the referenced path.

Path writeback rule (non-negotiable):
- Jab bhi koi artifact actual me write ho, uska final path hamesha CSV me save hona chahiye:
	- `Planning_Path` for planning artifacts
	- `Code_Path` for primary code artifact
	- `Doc_Path` for docs artifact
	- `Content_Path` for supporting content (CSS/JS/assets)
- `*_Save=1` ka matlab: artifact write karna AND corresponding `*_Path` fill karna mandatory hai.

Path prefill rule (NEW; NON-NEGOTIABLE):
- Agar planner me `*_Path` columns exist karti hain lekin blank hain, runner MUST:
	1) deterministically derive them (no guessing)
	2) write them back to the same CSV
	3) then execute the row

Reason:
- 1000+ PCs + OneDrive sync scenario me planner rows me paths missing hona execution blocker na bane.

CSV formatting rules (must, warna parsing toot sakta hai):
- Har cell quoted ho (sample jaisa): `"value"`
- Multiline `Description` allowed hai, lekin cell **double-quotes** me wrap ho aur inner `"` ko `""` (double quote escape) se escape karo.
- `Status` starts at `0` for actionable rows.

Compatibility note (Runner):
- Quote-all preferred, but runner MUST support standard CSV where only cells containing comma/newline/quote are quoted.

---

# [3.1] Deterministic Path Derivation (SSOT)
This mapping is used for `Planning_Path`, `Code_Path`, `Doc_Path`, `Content_Path` when missing.

SSOT references:
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

Version/Theme policy:
- Resolve WebApp `{Version}` as the latest numeric folder under `/Aliens/WebApp/{AppName}/`.
- If listing directories is not possible, fallback `{Version}=26.00.00`.
- `{AppTheme}={AppName}_Basic`.

Base:
- `{AppRoot}=/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}`
- Default role folder: `{AppRoot}/User/`.

### A) WebApp Structure
If `Worklflow=WebApp_Structure`:
- `Planning_Path` = `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/WebApp-Planning.md`
- `Code_Path` = `{AppRoot}/User/index.php`
- `Content_Path` = `{AppRoot}/User/css/0.css`
- `Doc_Path` = `/Aliens/Docs/WebApp/{AppName}/WebApp_Structure.md`

### B) WebApp Screen
If `Worklflow=WebApp_Screen`:
- `Planning_Path` = `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/Screen/{ScreenNameNormalized}.md`
- `Code_Path` = `{AppRoot}/User/screen/{ScreenNameNormalized}.php`
- `Content_Path` = `{AppRoot}/User/css/{ScreenNameNormalized}-Screen.css`
- `Doc_Path` = `/Aliens/Docs/WebApp/{AppName}/Screens/{ScreenNameNormalized}.md`

### C) WebApp Section
If `Worklflow=WebApp_Section`:
- `Planning_Path` = `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/Section/{SectionNameNormalized}.md`
- `Code_Path` = `{AppRoot}/User/section/{SectionNameNormalized}.php`
- `Content_Path` = `{AppRoot}/User/css/{SectionNameNormalized}-Section.css`
- `Doc_Path` = `/Aliens/Docs/WebApp/{AppName}/Sections/{SectionNameNormalized}.md`

### D) WebApp Component
If `Worklflow=WebApp_Component`:
- `Planning_Path` = `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/Component/{ComponentNameNormalized}.md`
- `Code_Path` = `{AppRoot}/User/component/{ComponentNameNormalized}.php`
- `Content_Path` = `{AppRoot}/User/css/{ComponentNameNormalized}.css`
- `Doc_Path` = `/Aliens/Docs/WebApp/{AppName}/Components/{ComponentNameNormalized}.md`

### E) WebApp Layout
If `Worklflow=WebApp_Layout`:
- `Planning_Path` = `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/Layout/{LayoutNameNormalized}.md`
- `Code_Path` = `{AppRoot}/User/layout/{LayoutNameNormalized}.php`
- `Content_Path` = `{AppRoot}/User/css/{LayoutNameNormalized}-Layout.css` (optional; if layout CSS is required)
- `Doc_Path` = `/Aliens/Docs/WebApp/{AppName}/Layouts/{LayoutNameNormalized}.md`

### F) WebApp Global CSS
If `Worklflow=WebApp_CSS` (or `WebApp_CSS_Update`):
- `Name` allowed:
	- Recommended: `{AppName}.Global`
	- Also allowed: `{AppName}` (runner derives AppName deterministically)
- `Planning_Path` = `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/CSS/{AppName}.Global.md`
- `Code_Path` = `{AppRoot}/User/css/0.css`
- `Content_Path` = blank (global CSS is the primary code artifact)
- `Doc_Path` = `/Aliens/Docs/WebApp/{AppName}/CSS/{AppName}.Global.md`

Hard rule:
- WebApp JS rows are forbidden; planner MUST use `Worklflow=WebSDK_Module_JS` for JS behavior.

---

# [4] Planner Generation Rules
- IDs must be sequential integers.
- Priority is numeric; higher runs first.
- Each row must be a single executable unit.
- `Worklflow` column must reference an existing ACC workflow id (examples: `WebApp_Screen`, `WebApp_Section`, `WebSDK_Module_API`).

Planner structure recommendations (quality gate):
- Row `ID=0` MUST be `__PlannerMeta__` (like sample). Ye row execution ke liye “global rules” encode karti hai.
- `__PlannerMeta__` row ka `Description` **ultra-detailed** hona non-negotiable hai. Is me clear define karo:
	- Is planner se exactly kya kya build hoga (WebApp + WebSDK scope)
	- UI information architecture (layouts/screens/sections/components)
	- Pixel-perfect UI expectations + acceptance gates
	- Global CSS governance (0.css) + token usage rules
	- Reusable components strategy (reuse-first)
	- Assets handling rule (sirf component rows me define)
- Har executable row (ID>=1) ka `Description` self-contained ho: it must include enough context + constraints so child workflow bina guess kiye 10/10 output de.
- Planner ka quality bar sample `B-Commerce.csv` se **higher** hona chahiye: more explicit context, acceptance criteria, security/perf/observability, and exact save-path contracts.

Pixel-perfect UI governance (NON-NEGOTIABLE):
- `WebApp_Layout`, `WebApp_Screen`, `WebApp_Section`, `WebApp_Component` workflows har unit ki **apni CSS** file generate karte hain (jitne units utni CSS). Is liye in rows ke `Description` me UI ko ultra-detail me describe karna mandatory hai.
- Exactly **ONE** global CSS row allowed:
	- Recommended: `ID=2` with `Worklflow=WebApp_CSS` (GLOBAL ONLY).
	- This row MUST generate/update `{AppRoot}/User/css/0.css`.
	- Additional `WebApp_CSS` rows (per-screen/per-section/per-component) are forbidden.
- Har unit row (Layout/Screen/Section/Component) ke `Description` me explicitly likho:
	- “Use tokens/variables/classes from `User/css/0.css`; no hardcoded colors/spacing where avoidable.”

Reusable components rule (NON-NEGOTIABLE):
- Planner me reusable `WebApp_Component` rows zaroor add karo (shared controls, cards, headers, list-items, empty-states, skeletons).
- Baaki components/sections/screens me duplication forbidden; reuse via `Component()` calls.

Assets rule (NON-NEGOTIABLE):
- Images/icons/fonts/any assets sirf `WebApp_Component` rows ke `Description` me define karo.
- Asset acquisition (create/download/place) `WebApp_Component` workflow handle karega; descriptions me deterministic source + expected local target paths + caching/bundling rules specify karo.

Enterprise baseline coverage (planner should include these categories unless scope explicitly excludes):
- `WebApp_Structure` row for scaffolding (or update structure when running update scenario).
- Documentation artifacts as first-class rows (PRD, UX flows, API contract SSOT, RBAC model, audit model, security model, observability model, deployment/runbook) so downstream code tasks never guess requirements.
- Security + governance defaults encoded once in `__PlannerMeta__`, and repeated only when a row needs stricter overrides.

WebApp JS placement rule (non-negotiable):
- WebApp ke andar custom JavaScript implement karna forbidden hai.
- If the app needs JS features, planner MUST create a module-level JS task row using `Worklflow=WebSDK_Module_JS`.
- Default module for WebApp JS is `User` unless the requirement explicitly states a different module.

UTag non-negotiable rule (ecosystem-level):
- Screen/Section/Component rows ke liye `utag` MUST be globally unique.
- Multi-PC safety ke liye manual UTag pick forbidden. Allocation MUST follow `Workflows/ACC/_Refs/WebApp_UTag.md`.
- Constraint: 1000+ PCs, no DB installed, no live sync.
- Solution: UTag v2 auto-mint (secure randomness) + local registry record (no human insert).

---

# [4.1] `Description` Prompt Contract (NON-NEGOTIABLE)
Har row ke `Description` me yeh structure minimum required hai. Isse kam nahi:

Required sections (exact headings recommended):
- `[OBJECTIVE]` (1-2 lines)
- `[NON-NEGOTIABLE STANDARDS]` (AliensStyle/AliensGrade/CleanCode + “no skeletons”)
- `[CONTEXT]` (AppName, Row Name/utag, Parent, Parent_Type, intent flags)
- `[ENTERPRISE QUALITY BAR 10/10]` (Architecture/Security/Performance/Maintainability/Scalability/Readability/Test-readiness/Observability/Backward-compat/CleanCode)
- `[OUTPUT CONTRACT]` (exact artifacts + exact save paths from `*_Path` columns)
- `[UI]` / `[DATA]` / `[STATES]` (only if relevant)
- `[OPERATIONS UX]` (confirmations, progress indicators, retry-safe actions, user feedback; no silent failures)
- `[MULTI-TENANT]` (tenant isolation assumptions + scoping rules; never UI-trust tenant context)
- `[RULES]` (security defaults, validation, authZ, encoding, caching, routing, hooks)
- `[ACCEPTANCE]` (clear pass/fail checks)
- `[DEPENDENCIES]` (DependsOn + required prerequisites)

WebApp UI rows additional mandatory rule:
- If `Worklflow` is `WebApp_Layout` / `WebApp_Screen` / `WebApp_Section` / `WebApp_Component` then `[UI]` section is NOT optional — it is mandatory and must include:
	- Visual style (density, spacing, typography scale)
	- Layout + responsiveness (<=999px, <=700px)
	- Interaction states (hover/active/focus/disabled/loading/error)
	- Token usage: variables/classes from `User/css/0.css` (no hardcoded hex/spacing unless explicitly justified)

WebApp Component rows additional mandatory rule:
- Add an `[ASSETS]` section when assets are needed (icons/images/fonts). Provide deterministic sources + local targets; forbid remote runtime `@import`/hotlinking by default.

If `Code=1` for that row, Description MUST include:
- Mandatory CodeHeader rule: first line exactly `# Copyright (c) A|iens. All rights reserved.`
- One blank line, then full structured CodeHeader (Engine/Module name, version, author, requires, responsibilities, dependencies, integration notes)
- Mandatory CodeFooter (versioning, hooks, future compatibility)

Planner-wide governance reminders (non-negotiable; encode in `__PlannerMeta__` and repeat per-row only if stricter):
- Parent chain contract is strict: Component -> Section -> Screen -> WebApp (no cross-parenting).

UI composition chain reminder (non-negotiable; practical meaning):
- Component actual me multiple standard HTML elements se mil kar banta hai.
- Section actual me multiple components ko compose karta hai (layout-only).
- Screen actual me multiple sections ko compose karta hai (composition-only).
- WebApp actual me multiple screens ko host karta hai (routing/wiring), UI hierarchy ko break nahi karta.

Planner row discipline (apply when building rows):
- Component row ka scope: sirf component file + css; is row me section/screen implement mat karo.
- Section row ka scope: sirf component calls + minimal wrappers; section ke andar component create/update mat karo.
- Screen row ka scope: sirf section calls + minimal wrappers; screen ke andar section create/update mat karo.
- Agar UI needs multi-level work ho (component + section + screen), to unko separate rows me split karo with DependsOn chain.
- Security baseline: session auth, CSRF protection for state changes, deny-by-default authZ, rate limits, safe defaults.
- Observability baseline: structured traces, deterministic logs, admin audit events.

Language/style constraints inside prompt:
- Romanized (no Hindi script).
- Deterministic instructions (no “maybe/try”).
- No secrets, tokens, credentials.

---

# [4.3] Recommended Row Prompt Templates (Better than Sample)
Use these as starting points for `ID>=1` rows. Har row me placeholders ko CSV columns se fill karo.

## [4.3.0] WebApp Global CSS row (example template)
Use when `Worklflow=WebApp_CSS` (GLOBAL ONLY):

"[OBJECTIVE]
Create the SINGLE global CSS design system for {AppName}. This must be pixel-perfect and reusable.

[NON-NEGOTIABLE STANDARDS]
- AliensStyle + AliensGrade + CleanCode. No skeletons.
- Exactly ONE global CSS row; output is `User/css/0.css`.

[CONTEXT]
- App: {AppName}
- Row: {Name}
- Parent: {Parent_Type} :: {Parent}

[OUTPUT CONTRACT]
- Primary CSS: {Code_Path} (must be `{AppRoot}/User/css/0.css`)
- Docs: {Doc_Path}

[CSS TOKENS / VARIABLES]
- Define full token system in `:root` (colors/surfaces/border/text/brand, spacing scale, radius, shadow, z-index, motion, breakpoints).
- Keep legacy base keys stable (`--a`, `--aa`, `--b`, `--bt`, `--c`, `--c0`, ...). Do not rename/delete.
- Provide alias tokens for readability (e.g., `--brand-primary: var(--a)` etc.).

[GLOBAL LAYOUT + UTILITIES]
- App shell primitives, containers, flex/grid helpers, scroll helpers, dividers.
- Overlay primitives (`popups`, `Flyouts`) and focus styles.

[BEHAVIOR]
- Button/input focus ring and interaction transitions.

[ACCEPTANCE]
- All UI units must be able to build pixel-perfect UI using only tokens/classes from 0.css + unit CSS.
"

## [4.3.1] Screen row (example template)
Use when `Worklflow=WebApp_Screen` / `WebApp_Screen-Code`:

"[OBJECTIVE]
Create a complete, production-ready WebApp Screen for {AppName}, deeply integrated with WebOS (not standalone).

[NON-NEGOTIABLE STANDARDS]
- AliensStyle + AliensGrade + CleanCode. No skeletons.
- Security + performance + observability ready. Multi-App/Multi-Tenant.

[CONTEXT]
- App: {AppName}
- utag: {utag}
- Row: {Name}
- Parent: {Parent_Type} :: {Parent}
- Intent: Planning={Planning}, Code={Code}, Docs={Docs}

[OUTPUT CONTRACT]
- Primary code: {Code_Path}
- Secondary content (if any): {Content_Path}
- Docs: {Doc_Path}

[UI]
- Define layout + primary actions, responsive behavior, and empty/loading/error/success states.

[DATA]
- Define required fields and data flow; prefer existing WebOS engines/modules.

[STATES]
- loading
- empty
- error
- success

[RULES]
- Security: output encoding, input validation, deny-by-default authZ, CSRF/session for state changes.
- Performance: avoid expensive loops; cache where appropriate; no N+1 patterns.
- Observability: d()/echo markers where applicable; trace_id; meaningful failure logs.

[ACCEPTANCE]
- Renders without warnings/errors.
- Responsive <=999px and <=700px.
- Accessibility basics.
- No obvious XSS/CSRF; authZ checks present.

[DEPENDENCIES]
- DependsOn: {DependsOn}
- Ref: {Ref}
"

## [4.3.2] WebSDK API row (example template)
Use when `Worklflow=WebSDK_Module_API` (or similar):

"[OBJECTIVE]
Implement an enterprise-grade API/module unit for WebOS/WebSDK integration.

[NON-NEGOTIABLE STANDARDS]
- AliensStyle + AliensGrade + CleanCode. No skeletons.
- Multi-tenant safe-by-default; validate inputs; output encoding; strict authZ.

[CONTEXT]
- App: {AppName}
- utag: {utag}
- Row: {Name}
- Parent: {Parent_Type} :: {Parent}
- Intent: Planning={Planning}, Code={Code}, Docs={Docs}

[OUTPUT CONTRACT]
- Primary code: {Code_Path}
- Docs: {Doc_Path}

[RULES]
- Include CodeHeader/CodeFooter requirements when Code=1.
- Use WebOS routing/config/logging/hooks/lifecycle where possible.

[ACCEPTANCE]
- Deterministic behavior; proper error model; no warnings.
- Test-readiness: stable contracts; mockable dependencies.

[DEPENDENCIES]
- DependsOn: {DependsOn}
- Ref: {Ref}
"

## [4.3.3] WebSDK Module JS row (example template)
Use when `Worklflow=WebSDK_Module_JS` (module-level JS; preferred for WebApp JS needs):

"[OBJECTIVE]
Implement enterprise-grade module JS for WebApp needs (no WebApp-local JS).

[NON-NEGOTIABLE STANDARDS]
- AliensStyle + AliensGrade + CleanCode. No skeletons.
- Security/performance/observability ready. Multi-tenant safe-by-default.

[CONTEXT]
- App: {AppName}
- Module: {Name} (default: User)
- Parent: {Parent_Type} :: {Parent}
- Intent: Planning={Planning}, Code={Code}, Docs={Docs}

[OUTPUT CONTRACT]
- Module PHP entry (must exist / be verified): /Aliens/WebSDK/PHP.26.00.00/Module/{Name}/php/index.php
- Primary JS: {Code_Path} (must be /Aliens/WebSDK/PHP.26.00.00/Module/{Name}/js/index.js)
- Docs: {Doc_Path}

[RULES]
- JS class name MUST be exactly {Name}.
- Export: default export required; named export recommended.
- No secrets, no noisy console logs.

[ACCEPTANCE]
- `js/index.js` defines class `{Name}` and exports it (default).
- No WebApp files contain custom JS logic.

[DEPENDENCIES]
- DependsOn: {DependsOn}
- Ref: {Ref}
"

---

# [4.2] Recommended `__PlannerMeta__` Prompt (Template)
Use this as the `Description` for row `ID=0` (customize AppName):

"[OBJECTIVE]
Generate COMPLETE, PRODUCTION-READY, ULTRA ENTERPRISE-GRADE output for this Planner row, deeply integrated with WebOS ecosystem (not standalone).
Quality bar: Multi-App, Multi-Tenant, High-Performance, Secure-by-default, Future-proof, AI-auditable (target 10/10).

[NON-NEGOTIABLE STANDARDS]
- AliensStyle + AliensGrade + CleanCode must be applied.
- Naming: enterprise-grade; clear responsibilities; zero ambiguity.
- No placeholder/skeleton output.
- Prefer existing WebOS engines/modules/routing/config/logging/hooks/lifecycle where possible.

[CONTEXT]
- App: {AppName}
- Row: Meta :: __PlannerMeta__

[ENTERPRISE QUALITY BAR 10/10]
Architecture | Security | Performance | Maintainability | Scalability | Readability | Test-readiness | Observability-readiness | Backward compatibility | CleanCode

[RULES]
- Security: validate/sanitize inputs, output encoding, deny-by-default authZ, CSRF/session protection for state-changing actions.
- Performance: avoid N+1, cache sensibly, avoid expensive loops in render path.
- Observability: structured logs (d()/echo markers where applicable), trace_id correlation, actionable failure logs.
- Usability: sensible defaults, minimal configuration, extensible hooks.

[ACCEPTANCE]
- No PHP warnings/errors (where applicable).
- UI responsive (<=999px, <=700px) without layout break.
- Accessibility basics (labels/focus/keyboard/contrast).
- Security checks: no obvious XSS/CSRF holes; proper authZ.
"

---

# [5] Save Outputs
- Write the CSV to `/Aliens/Project/{Developer_Username}/{AppName}_Planner.csv`.

---

# [6] Errors
- `WF_PLANNING_REQUIRED`
- `WF_PERMISSION_DENIED`
