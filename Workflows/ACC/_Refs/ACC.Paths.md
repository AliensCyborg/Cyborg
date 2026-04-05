---
RefId: ACC.Paths
Type: ACC.Reference
Scope: PathResolution
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-24
Audience: "AlienCyborg Agent + ACC Workflow Authors"
Purpose: >
  Define a single, consistent, future-proof way to resolve ALL filesystem paths inside Aliens Ecosystem.
  This ref is used by Workflow_Plural + Workflow_Singular-* common files and by all individual workflows.
NonNegotiables:
  - NoDelete: true
  - DefaultTheme: "_Basic"
  - DefaultVersionStrategy: "latest (fallback PHP.26.00.00)"
---

# ACC.Paths.md

## [01] Overview (Hinglish)
Is reference ka kaam: **kisi bhi ACC Workflow ko exact path batana** ki files kahan read/write hongi.  
Goal: **zero confusion**, **same pattern everywhere**, aur future me version/theme/structure change ho to bhi ref update karke sab workflows ko automatically benefit mil jaye.

Is file me:
- Aliens root paths
- WebOS engines (PHP/JS/SQL/Bash) ke paths
- WebApps (Theme, layout, component, css, template) ke paths
- WebSDK (Module, api, cron, webhook, sql, php) ke paths
- WebBrand (Brand Theme, app-prefixed layout files) ke paths
- Docs + .Alien developer workspace paths

> Important: **WorkflowId hi decide karta hai target ka type kya hai.**  
> Example: `WebSDK_Module_API` me `User/UserNew` => Module + API.  
> Example: `WebApp_Component` me `Exchange/Balance` => App + Component.

---

## [02] Root Paths (Aliens Ecosystem)
These are canonical roots (as per your defined structure):

- `AliensRoot` = `/Aliens`
- `ProjectRoot` = `/Aliens/Project`
- `WebOSRoot` = `/Aliens/WebOS`
- `WebAppRoot` = `/Aliens/WebApp`
- `WebSDKRoot` = `/Aliens/WebSDK`
- `WebBrandRoot` = `/Aliens/WebBrand`
- `DocsRoot` = `/Aliens/Docs`
- `AlienRoot` = `/Aliens/.Alien` *(per-developer workspace repo)*

### Developer Workspace Paths (per developer)
- `DevRoot` = `/Aliens/.Alien/{Developer_Username}`
- `DevPlanningRoot` = `/Aliens/.Alien/{Developer_Username}/Planning`
- `DevTaskRoot` = `/Aliens/Project/{Developer_Username}/` *(planner/tasks CSV SSOT root)*
- `DevContentRoot` = `/Aliens/.Alien/{Developer_Username}/Content`
- `DevMemoryRoot` = `/Aliens/.Alien/{Developer_Username}/Memory` *(conversation/task memory; scoped & guarded; see `ACC.Memory`)*
- `DevRunsRoot` = `/Aliens/.Alien/{Developer_Username}/Runs`
- `DevApprovalsRoot` = `/Aliens/.Alien/{Developer_Username}/Approvals`

### Planner CSV Paths (SSOT)
- `PlannerCsvRoot` = `/Aliens/Project/{Developer_Username}` *(planner/tasks CSVs live here; do not pick CSVs from elsewhere when workflow provides a CSV name)*

Additional recommended roots:
- `DevResearchRoot` = `/Aliens/.Alien/{Developer_Username}/Research`
- `DevAuditRoot` = `/Aliens/.Alien/{Developer_Username}/Audit`
 
---

## [03] Version Resolution Rules
ACC should always try to use **latest** version, but if auto decide possible nahi:

- Default fallback version = `PHP.26.00.00`

### How to resolve “latest” (policy)
- If a root contains multiple versions (e.g., `PHP.25.xx.xx`, `PHP.26.00.00`), choose highest.
- If cannot list directories (permissions/tooling limitation), use fallback `PHP.26.00.00`.
- Workflow YAML may override version if explicitly provided.

---

## [04] Theme Resolution Rules
- Default theme name = `_Basic`
- Apps: `{AppName}_Basic`
- Brands: `{BrandName}_Basic`
- Long time tk `_Basic` hi valid baseline rahega (as you stated).

---

## [05] Target Grammar to Path Inputs (minimal)
Target strings can look like:
- `WebOS.Cache`
- `User/UserNew`
- `Exchange/Balance`
- `Exchange/Home`

Parsing:
- `Comma` splits into multiple independent runs.
- `Slash` splits Parent/Child.
- `Dot` (WebOS.EngineName) is used in WebOS engine naming, not a file extension.

> File extension workflow decide karega (php/js/sql/sh). Target me extension kabhi nahi hoti.

---

## [06] WebOS Engine Paths
Base concept:
- EngineBaseName example: `WebOS.Cache` (no extension)
- Language/Unit decides folder + extension.

### PHP Engines
- Path: `/Aliens/WebOS/{Version}/Engine/{EngineBaseName}.php`
Example:
- `/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Cache.php`

### JS Engines
- Path: `/Aliens/WebOS/{Version}/Unit/js/{EngineBaseName}.js`
Example:
- `/Aliens/WebOS/PHP.26.00.00/Unit/js/WebOS.Cache.js`

### Bash Engines
- Path: `/Aliens/WebOS/{Version}/Unit/bash/{EngineBaseName}.sh`
Example:
- `/Aliens/WebOS/PHP.26.00.00/Unit/bash/WebOS.Cache.sh`

### SQL Engines
- Path: `/Aliens/WebOS/{Version}/Unit/sql/{EngineBaseName}.sql`
Example:
- `/Aliens/WebOS/PHP.26.00.00/Unit/sql/WebOS.Cache.sql`

---

## [07] WebApp Paths (Theme-based)
Base:
- App install root: `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}`
- Default AppTheme: `{AppName}_Basic`

Naming inputs (SSOT; non-negotiable):
- For WebApp UI artifacts (Layout parts / Screen / Section / Component), the `{*Name}` placeholders below must already be **normalized basenames** per:
  - `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`
- Meaning:
  - Dot-prefix basenames for new work: `AppName.<Basename>`
  - No double-prefix
  - Legacy underscore inputs may be accepted and normalized by the workflow/runner

Role folders (common):
- `{AppRoot}/User/`
- `{AppRoot}/Admin/`
- `{AppRoot}/Manager/`
- `{AppRoot}/Marketer/`

SSOT default for ACC WebApp workflows:
- Use `User/` unless explicitly specified otherwise.

### Layout
- Path (layout part PHP): `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/layout/{LayoutName}.php`
Example:
- `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/layout/Exchange.Nav.php`

Notes:
- Layout parts live under `User/layout/` and are included by `User/layout.php`.
- For new work, layout part basenames must be app-prefixed (dot): `Exchange.Nav.php`.

### Screen (PHP)
- Path: `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/screen/{ScreenName}.php`

Example:
- `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/screen/Exchange.Home.php`

### Section (PHP)
- Path: `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/section/{SectionName}.php`

Example:
- `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/section/Exchange.Home_Header.php`

### Component (PHP)
- Path: `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/component/{ComponentName}.php`
Example:
- `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/component/Exchange.Balance.php`

### CSS (WebApp Theme)
- Path: `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/css/{CssName}.css`

CSS naming rule (SSOT):
- Type-specific CSS naming (Screen/Section/Layout suffix rules; Component no suffix) is governed by:
  - `Workflows/ACC/_Refs/WebApp_Naming_and_Screens.md`

Examples (SSOT-aligned):
- Component CSS: `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/css/Exchange.Balance.css`
- Screen CSS: `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/css/Exchange.Home-Screen.css`
- Section CSS: `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/css/Exchange.Home_Header-Section.css`
- Layout CSS (optional): `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/css/Exchange.Nav-Layout.css`

### Templates
- Template root: `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/template/{TemplateName}/`
Example:
- `/Aliens/WebApp/Exchange/26.00.00/theme/Exchange_Basic/User/template/Home/`
Common sections inside template (examples):
- `header.php`, `area.php`, `footer.php`, `CTA.php`, etc.

---

## [08] WebSDK Paths (Modules)
Base:
- WebSDK root: `/Aliens/WebSDK/{Version}/Module/{ModuleName}/`

### Module Class (PHP)
As per your rule: 95% cases me single class hoti hai.
- Path: `/Aliens/WebSDK/{Version}/Module/{ModuleName}/php/index.php`
Example:
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/php/index.php`

### API
- Path: `/Aliens/WebSDK/{Version}/Module/{ModuleName}/api/{ApiName}.php`
Example:
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/api/UserNew.php`

### Cron
- Path: `/Aliens/WebSDK/{Version}/Module/{ModuleName}/cron/{CronName}.php`
Example:
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/cron/Users.php`

### Webhook
- Path: `/Aliens/WebSDK/{Version}/Module/{ModuleName}/webhook/{WebhookName}.php`
Example:
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/webhook/UserUpdate.php`

### SQL (module related)
- Path: `/Aliens/WebSDK/{Version}/Module/{ModuleName}/sql/index.sql`
Example:
- `/Aliens/WebSDK/PHP.26.00.00/Module/User/sql/index.sql`

### Future folders (allowed)
- `/component/`, `/css/`, `/template/` may exist inside module (future expansion).

---

## [09] WebBrand Paths (Brand Theme)
Base:
- Brand install root: `/Aliens/WebBrand/{BrandName}/{Version}/Theme/{BrandTheme}`
- Default BrandTheme: `{BrandName}_Basic`

### Layout (Brand override)
Rule: Brand layout file me **AppName prefix mandatory**.
- Path: `/Aliens/WebBrand/{BrandName}/{Version}/Theme/{BrandTheme}/layout/{AppName}.{LayoutName}.php`
Example:
- `/Aliens/WebBrand/Binance/PHP.26.00.00/Theme/Binance_Basic/layout/Exchange.Nav.php`

### Component
- Path: `/Aliens/WebBrand/{BrandName}/{Version}/Theme/{BrandTheme}/component/{ComponentName}.php`

### CSS
- Path: `/Aliens/WebBrand/{BrandName}/{Version}/Theme/{BrandTheme}/css/{ComponentName}.php`

### Templates
- Path: `/Aliens/WebBrand/{BrandName}/{Version}/Theme/{BrandTheme}/template/{TemplateName}/`

> Brand ka behavior: agar same-name file Brand me exist karti hai, to App wali file override ho jati hai.

---

## [10] Docs Paths (Mandatory outputs)
Docs must be saved under `/Aliens/Docs/` with domain buckets:

Recommended domain buckets:
- WebOS docs: `/Aliens/Docs/WebOS/`
- WebSDK docs: `/Aliens/Docs/WebSDK/`
- WebApps docs: `/Aliens/Docs/WebApps/{AppName}/`
- WebBrand docs: `/Aliens/Docs/WebBrand/{BrandName}/`
- ACC system docs: `/Aliens/Docs/ACC/`

Planning outputs (developer-local):
- `/Aliens/.Alien/{Developer}/Planning/`

Run-record outputs (developer-local):
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/...`

---

## [11] Update Workflow Path Rule (Modify-only)
If WorkflowId contains `_Update`:
- Target MUST exist at resolved path
- If missing: **error message** (do not create)
- If exists: modify allowed

If workflow is NOT `_Update`:
- Create if missing is allowed (still NoDelete applies)

---

## [12] Edge Rules / Validation Notes
- Never allow `..` traversal in any resolved path.
- App/Brand/Module/Engine names should be validated against safe filename rules.
- If a target contains spaces, normalize or reject based on ACC.Targets.md policy.
- Always log resolved paths in RunRecord for audit.

---

## [13] Quick Reference Table (mental map)
- WebOS Engine:
  - PHP: `WebOSRoot/{V}/Engine/{E}.php`
  - JS:  `WebOSRoot/{V}/Unit/js/{E}.js`
  - SH:  `WebOSRoot/{V}/Unit/bash/{E}.sh`
  - SQL: `WebOSRoot/{V}/Unit/sql/{E}.sql`

- WebApp Theme:
- Root: `WebAppRoot/{App}/{V}/Theme/{App}_Basic/`
  - layout: `layout/{Name}.php`
  - component: `component/{Name}.php`
  - css: `css/{Name}.php`
  - template: `template/{TemplateName}/...`

- WebSDK Module:
  - Root: `WebSDKRoot/{V}/Module/{Module}/`
  - class: `php/index.php`
  - api: `api/{Name}.php`
  - cron: `cron/{Name}.php`
  - webhook: `webhook/{Name}.php`
  - sql: `sql/index.sql`

- WebBrand Theme:
  - Root: `WebBrandRoot/{Brand}/{V}/Theme/{Brand}_Basic/`
  - layout: `layout/{App}.{Name}.php` (App prefix mandatory)

---

## [14] Changelog
- 26.00.00 (2025-12-24): Initial ACC Paths reference aligned with Aliens Ecosystem structure.
