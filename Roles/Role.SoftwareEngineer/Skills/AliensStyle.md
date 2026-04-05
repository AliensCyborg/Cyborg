# Copyright (c) A|iens. All rights reserved.

---
```yaml
DocumentID: AliensStyle
Title: AliensStyle (Enterprise Ultra Standard)
Owner: A|iens Ecosystem
RoleScope: Role.SoftwareEngineer
AppliesTo:
  - WebOS_Core
  - SDK
  - Apps
  - Brand
  - Unit
  - Engine
Languages:
  - PHP
  - JavaScript
  - SQL
  - Markdown
Version: 26.12.24-AU5e
Author: "AlienCyborg"
AuthorURI: "https://aliens.com"
Requires:
  PHP: "7.4+"
  Notes:
    - Follow AliensStyle v ChatGPT v10.0.0 compatibility baseline unless a specific project explicitly requires a higher PHP version.
CoreRules:
  Naming: PascalCase


### 3.6 Logger Guard & Replay Pattern (Auth/Security Mirror)
- **WebOS Engines**: Never assume `d()` or `x()` exists—wrap every call with `function_exists` like WebOS.Auth does:
  - `if(function_exists('d')){ $echo = 'Func() : ...'; d($echo, __LINE__, 'progress'); }`
  - `if(function_exists('x')){ $echo = 'Func() : ...'; x($echo, __LINE__, 'progress'); }`
- **WebSDK Modules**: Do **not** add `function_exists()` guards for core ecosystem primitives (`d`, `Row/Rows/RowNew/RowUpdate/RowStatus`, `Access`, `import`). In Aliens runtime these are guaranteed; defensive guards are the platform/boot responsibility.
- Helper wrappers (`self::Dbg`, `self::Log`, etc.) must accept `$echo` and perform the same guard internally so the echo string is never duplicated inline. Always pass the identical `$echo` string you declared earlier.
- When logging is unavailable, drop to `error_log($echo);` exactly once per section to mimic the resiliency of the attached engines—never leave the block silent.
- Treat `$echo` as a reusable debug narrative: declare once, reuse across `d()`, `x()`, and helper loggers. This mirrors how WebOS.Security pipes `$echo` into `self::Dbg($echo);` before returning guard decisions.

### 3.15 PascalCase Everywhere (Except Keys/Columns)
- Use **PascalCase** for: class names, public method names, helper names, variables, constants, doc headings.
- Exceptions:
  - DB column names
  - payload keys / request keys
  - legacy DSL keys used by AliensCRUD (`oprator`, `value1`, `value2`, `LIMIT`, `OFFSET`, etc.)

### 3.16 WebSDK Module CRUD Naming (Row Family Mirror)
- Module CRUD method names must mirror AliensCRUD Row family:
  - `Teacher()` → `Row()`
  - `Teachers()` → `Rows()`
  - `TeacherNew()` → `RowNew()`
  - `TeacherUpdate()` → `RowUpdate()`
  - `TeacherStatus()` → `RowStatus()`
- When a module uses **2+ tables**, the secondary/cluster tables MUST use underscore prefix naming:
  - `Teacher_Attendance()`, `Teacher_Attendances()`, `Teacher_AttendanceNew()`, `Teacher_AttendanceUpdate()`, `Teacher_AttendanceStatus()`

### 3.17 Function Internal CodeHeader (Required)
- Every function body must start with an internal **function code-header** block describing:
  - purpose + steps
  - required inputs
  - outputs/return shape
  - which Row-family helper is used (if applicable)
- **Placement law (strict):**
  - Section divider stays **outside** the function (above the signature)
  - Function code-header stays **inside** the function body
  - The internal code-header must appear immediately after the opening `$echo` log (or be the first statement if no `$echo` is required)
- Minimum template (WebSDK modules):
```php
// ============================================================================
// [SECTION] Module::MethodName()
// ============================================================================
public function MethodName($Q=null){
  $echo = '[Module][MethodName] '; d($echo, __LINE__, 'process');
  /* [CODEHEADER]
    Purpose : <one line>
    Input   : <keys/shape>
    Output  : <return shape>
    Engine  : Row/Rows/RowNew/RowUpdate/RowStatus or mysqli/other
  */
  // ...logic...
}
```

### 3.20 Section Divider Standard (Mandatory)
- Every public method in WebSDK modules must be preceded by a **SECTION divider** block.
- Divider format (exact):
```php
// ============================================================================
// [SECTION] Module::MethodName()
// ============================================================================
```
- Divider must appear:
  - directly above the function signature (no extra unrelated comments in between)
  - for every public method (including `__construct()`)
- **Do not** place the internal function code-header outside the function; only the divider goes outside.

### 3.21 English-Only Comments & Logs (Mandatory)
- In WebSDK modules, **all developer comments** and **internal `$echo` log strings** must be written in **clear English**.
- This rule applies to:
  - `// ...` and `/* ... */` developer comments
  - `$echo = '...'` observability/debug strings used with `d()` / `x()`
- Exception:
  - User-facing UI text (rendered to end-users) may be localized, but must not be mixed into internal observability logs.
- Reason:
  - Keeps code reviews, SOC replay traces, and cross-team debugging deterministic across the ecosystem.

### 3.18 Static Helper Law (Modules)
- In modules, **helpers** may be `static` if they are pure utilities.
- **Important/public API methods** must never be `static`.

### 3.19 Database Prefix Law (WebSDK)
- Database table prefix (example: `Coinpods_`) is **environment-owned** and can change.
- WebSDK modules must **never** hardcode full table names with prefix.
- Always pass **base table names only** into Row-family calls, e.g.:
  - `teacher` (not `Coinpods_teacher`)
  - `teacher_attendance` (not `Coinpods_teacher_attendance`)
- Reason: Row-family resolves the real table using runtime prefix (see WebOS `Row()` pattern: `$TableFullName = prefix.prefix1.$Table`).

### 3.7 Echo Copy Format (Boot/Security Mirror)
- Prefix every `$echo` with the component + section exactly like `[Boot] Phase: InitPaths` or `Security.Guard : Allowed`. The tag must match the surrounding section header so log search stays deterministic.
- Keep the verb short (`Started`, `Inputs captured`, `Calling ModuleClass::Method()`) and avoid runtime concatenation; dynamic values should be logged via structured arrays, not appended strings.
- For helper delegates (SafeLog, Dbg), reuse the same `$echo` string. Never craft a second message when proxying—this keeps Boot/Security style “Started/Allowed” traces consistent.
- When loops require granular logs, suffix the iteration info after the colon (e.g., `[Cache] UID() : Arg#1 normalized`). Follow WebOS.Cache’s style where the colon separates the phase label and the short action.

### 3.8 Echo QA Checklist (Auth/Boot/Security Mirror)
- Every public method MUST start with a `$echo` log before performing work, just like WebOS.Auth’s `Context()`, `ValidateSession()`, `IssueSignedToken()` etc. Private helpers may skip logging only if the caller already logged the action.
- When a method returns early (guards, errors), emit another `$echo` line describing the branch before returning, mirroring WebOS.Security’s “Denied”/“Allowed” execution trail.
- Paired operations (Start/Finish) should log both sides to trace durations (Boot logs START + Finalize). If a function performs heavy work, add a completion log with the same prefix.
- QA must verify `$echo` casing, prefix, and guard usage match the WebOS sample set before approving any file; mismatched casing (`[boot]` vs `[Boot]`) fails style review.

### 3.9 Echo & Helper Fallback Law (WebOS.Cache/Security Proof)
- Every logging helper (`d`, `x`, `Dbg`, `SafeLog`) must first check `function_exists()` and gracefully degrade in this order: preferred helper → alternate helper (SDK switches to `d()`, core switches to `x()`) → `error_log()` as the absolute fallback. This mirrors how WebOS.Cache and WebOS.Security refuse to throw notices when log helpers are missing.
- Helper wrappers MUST NOT mutate `$echo`; they simply forward it. If a helper needs extra context, add it before the helper call so all fallbacks share the same narrative line.
- When both SDK + WebOS helpers are unavailable (rare CLI contexts), emit exactly one `error_log($echo);` per section to avoid noisy duplicates. Tests must confirm this branch never crashes the script.
- Document the fallback order inside headers/footers whenever a module defines custom log helpers so auditors immediately see how observability stays alive.

### 3.10 Legacy SDK Files — Minimal Instrumentation
- When editing legacy SDK `php/index.php` files that previously lacked structured logging, add minimal `$echo` + `d($echo, __LINE__, '<type>')` logs at major entry/exit points without refactoring control flow.
- Do not remove existing inline `$echo` placeholders or developer banners; simply augment with guarded `d($echo, __LINE__, '<type>')` calls to improve traceability while preserving behavior.

### 3.11 Module Prefix Format (Account Mirror)
- WebSDK module logs MUST prefix every `$echo` with `[Module][Method]` (or `[Module][Section]`) exactly like `[Account][Accounts] Execute query.` so Router traces line up with facade names.
- The module tag mirrors the class name (for example `Account`), while the second tag mirrors the current method or section; keep both in PascalCase or SnakeCase exactly as the public method name to preserve grep-ability.
- When emitting sub-steps inside the same method, keep the prefix unchanged and vary only the action message so SOC reviewers can collapse logs per method.

### 3.12 Bootstrap Log Pair (Account Mirror)
- Immediately after the Access gate and before hefty work, emit at least two bootstrap logs: a neutral `[Module][Bootstrap]` marker and a descriptive follow-up such as `Access gate: initializing ...` just like the Account module.
- Use this pair to document module provenance (which module was imported and why the bootstrap is running) so SDK orchestrators can confirm the guard order without opening the file.
- Keep bootstrap logs at the very top (before `import()` calls) and reuse the same `[Module][Bootstrap]` prefix whenever the bootstrap performs follow-up tasks (version stamps, manifest population, and similar chores).

### 3.13 Start/Finish Echo Pair (Module Mirror)
- Every public/protected method must emit a `[Module][Method] Started` log before touching any payload and a matching `[Module][Method] Completed` (or `[Module][Method] Failed : ...`) log immediately before each return. Severity should graduate from `progress` to `success`/`error`, mirroring the Account module trace pairs.
- Guard-heavy methods with multiple exits must log the exact branch reason using the same prefix (e.g., `[Teacher][TeacherNew] Guard : Missing TeacherId`). Skipping the closing log on even one branch fails QA and breaks replay traces.
- Helper delegates (`RowNew`, `RowUpdate`, `RowStatus`) may reuse the caller’s `$echo`, but the caller still owns the opening/closing pair so trace visualisations stay balanced.

### 3.14 Branch Outcome Replay (Row Mirror)
- Any branch that mutates data (`Row*`, `Rows*`, `UID*`) requires a before/after log so reviewers can replay payload evolution without running the code. Example: `[Teacher][TeacherUpdate] RowUpdate : Payload ready` before the helper and `[Teacher][TeacherUpdate] RowUpdate : Completed` after a truthy result.
- When looping (bulk attendance sync, batch status updates) suffix the `[Module][Method]` prefix with the loop token (`: Item #{$Index}`) exactly like the Account module’s pagination logging so per-item behavior can be isolated.
- Early aborts (invalid payload, duplicate rows) must still log the branch outcome before returning, even if the caller also logs. This mirrors WebOS.Security’s “Denied because …” cadence and prevents silent branches.

---

## 4) File Structure Standard (OS‑Grade)

### 4.1 No Mixing Concerns
- `api/` = request handling + payload building + calling class function
- `class/` or module class = business logic
- `cron/` = scheduled tasks only
- `webhook/` = webhook entry only
- `component/` = reusable UI building blocks
- `template/` = pure presentation

### 4.2 Standard Module Layout
```
ModuleName/
  api/
  component/
  template/
  cron/
  webhook/
  js/
  css/
  sql/
  doc/
  config.json
```

### 4.3 Module vs Engine Boundary Law
- WebSDK modules are consumer classes, not WebOS engines. They MUST stay instance-based (`$ModuleObject = new Module();`) and must never expose `WebOS::Something()` facades, Router Maps, or `public static function Main()` entry points—those belong exclusively to `/Aliens/WebOS/*` engines.
- Access/import/bootstrap responsibilities live in SDK entry files. Do not reimplement Boot/Engine duties (error handlers, manifest hydration, EngineRouter payload parsing) inside module classes. Modules may call engines but cannot replace them.
- Engine-only helpers (`x()` logging, EngineRouter payload structs) are prohibited inside `/WebSDK/Module/*` code. If a module genuinely needs an engine feature, inject it as a documented dependency and log the hand-off explicitly.

---

## 5) AliensCodeHeader & AliensCodeFooter (Mandatory)

### 5.1 CodeHeader Minimum
Every generated/updated file MUST begin with:

1) First line (mandatory):
```php
# Copyright (c) A|iens. All rights reserved.
```

2) Then a structured header block with:
- File path
- Purpose
- Version
- Author + Author URI
- Requires PHP
- Dependencies list
 - Version Footprint block per Section 5.3a (AliensStyle, LastModified UTC, and relevant WebOS/WebSDK/App versions)

### 5.2 CodeFooter Minimum
Must include:
- Usage guide (URL example if API)
- Notes (compatibility, logging used, envelope used)
- Change log (if update/patch)

### 5.3 Extended Header Format (Engines + Enterprise APIs)
- Wrap the header inside ASCII bars just like the WebOS engines:
  - `/* ============================================================================` on top and bottom.
  - Inner divider `------------------------------------------------------------------------------` to separate metadata chunks.
- Mandatory metadata blocks (all of them):
  1. `[HEADER] <Name>` with version line (`Version : PHP.26.00.00` style).
  2. `Location` (absolute path) + `Engine Type` or `File Type`.
  3. `Purpose` list with bullet points (each prefixed by `-`).
  4. `Highlights` list (at least three crisp bullets).
  5. `Dependencies` list grouped as in the sample files (Engines, Globals, Helpers).
  6. Optional `Router Map` or `Calls` block when the file is used via EngineRouter/WebSDK.
  7. `Status` + `Author` + `Notes` block.
- Include blank lines between logical groups to keep the metadata scannable even when the block is long.

### 5.3a Header Version Footprint & Last Modified (Mandatory)
- Every CodeHeader must also include a compact version footprint and timestamp block. This enables auditability across ecosystems.
- Required lines (names must match exactly):
  - `AliensStyle   : <doc-version>`  // e.g., 26.12.20-AU5b (the AliensStyle version used when writing or last editing this file)
  - `LastModified  : <UTC ISO8601>`  // e.g., 2025-12-20T10:35:00Z
  - `WebOS         : <version>`      // required for files under /Aliens/WebOS/* (e.g., PHP.26.00.00); else `N/A`
  - `WebSDK        : <version>`      // required for files under /Aliens/WebSDK/*; else `N/A`
  - `App           : <version>`      // required for files under /Aliens/WebApps/<App>/*; else `N/A`
- Selection rules:
  - If a file lives in multiple contexts (rare), include all applicable lines (e.g., an SDK adaptor inside an App may include both `WebSDK` and `App`).
  - When a version is unknown at authoring time, set `N/A` explicitly—never omit the line.
  - The timestamp MUST be in UTC with a trailing `Z`. Prefer ISO 8601 format.
  - Teams may extend with additional product lines (e.g., `Brand`, `Agent`) but the above five lines are mandatory.

```
/* ============================================================================
 [HEADER] ModuleName.Endpoint (Enterprise API)
 ------------------------------------------------------------------------------
 Version     : PHP.26.00.00
 Author      : Aliens Company (WebOS)
 Location    : /Aliens/WebOS/PHP.26.00.00/Engine/ModuleName.Endpoint.php
 AliensStyle : 26.12.20-AU5d
 LastModified: 2025-12-20T10:35:00Z
 WebOS       : PHP.26.00.00
 WebSDK      : N/A
 App         : N/A
 Purpose     :
   - Item 1
   - Item 2
 Highlights  :
   - Highlight 1
 Dependencies:
   - Engine: WebOS::Cache()
   - Globals: Aliens, App, i
 Router Map  : <describe>
 Notes       : <extra guidance>
 ============================================================================ */
```

### 5.4 Extended Footer Format (Usage Playbook)
- Footer must mirror the richness of the WebOS sample files:
  - `[CODE FOOTER] Usage Guide` with numbered or bulleted steps that show different contexts (API boot, CLI, cache usage, etc.).
  - `Options` or `Advanced` sub-blocks when multiple modes exist (e.g., Cache modes, Security decisions).
  - Mention helper calls and their parameters exactly as they appear in code so a reader can copy-paste.
- Encourage multiple concrete examples (minimum two) covering typical + power usage.
- If the file exposes router entry points, summarize the map again in the footer for quick recall.

### 5.5 Router Payload + Call Map Blocks (Engine Law)
- Any EngineRouter-exposed file (Cache, Manifest, Security, Context, Auth) must dedicate a header subsection for both **Router Map** and **Router Payload Format** exactly like the WebOS.Cache sample.
- `Router Map` lines must show facade call → class method mapping for every public variant (Main/Get/Delete/... ).
- `Router Payload Format` (or `Entry Payload Format`) must enumerate payload indexes/keys, describe expected types, and mention optional flags such as `force_fresh`, `ttl`, `allow_guest`.
- If the file also serves HTTP/CLI entry points, show equivalent request parameters so Boot/Router teams can trace flows without opening the code.

### 5.6 Status + Ownership Ledger (Audit Ready)
- Extended headers must end with a ledger capturing `Status`, `Author`, `Maintainer`, `LastAudit`, and `Notes`, mirroring the closing rows inside WebOS.Auth and WebOS.Security headers.
- When multiple teams co-own a file, list each role (`Author`, `Reviewer`, `Maintainer`) on its own line for SOC2 evidence.
- Update `Status` whenever the module graduates from Beta→Stable, and reflect that change again inside the footer Change Log.

### 5.7 Header Quality Checklist (Auth/Boot/Security Mirror)
- Purpose block must contain **one bullet per responsibility** (Auth sample shows identity context, session validation, token parsing, signed tokens). Do not merge responsibilities into a single sentence.
- Highlights require at least three bullets and must call out behavior, not marketing (“Deny-by-default security mindset”, “Boot-friendly sequence”, “High-performance: minimal DB assumptions”).
- Dependencies must be grouped e.g., `Engines`, `Globals/Consts`, `Helpers`, `Optional`. Each line should mention how the dependency is used ("WebOS::Security() — optional: IP/UserAgent checks").
- When the engine is callable through routers, place `Router Map` and `Router Payload Format` immediately after dependencies and format them with ASCII dividers exactly like WebOS.Cache.
- Close the header with the `Status/Author/Maintainer/LastAudit/Notes` ledger plus any warranty statement (“DB tables are stub-ready”) before entering the class/function body.

### 5.8 Footer Encyclopedia Requirements (Cache/Manifest Mirror)
- `[CODE FOOTER] Usage Guide` must include at least two numbered workflows (e.g., Boot entry + CLI helper) and quote the actual function names plus sample arguments (`WebOS::Auth()->ValidateSession(['allow_guest'=>false])`).
- Add an `[OPTIONS]` or `[ADVANCED]` subsection enumerating flags, defaults, and side-effects (TTL defaults, `allow_guest`, `bind_tenant`, `force_fresh`, etc.).
- For engines exposing multiple entry points, restate the Router Map inside the footer so devs copy-paste without scrolling.
- Include contextual notes such as cache scope formulas, expected envelopes, or error formats (WebOS.Security’s “public/private/sensitive” cache notes, WebOS.Cache’s three mode explanations).
- Footers should end with a final ASCII divider and, if relevant, a `[CHANGE LOG]` snippet that mirrors the header ledger for incremental diffs.

### 5.9 Header/Footer Mirror Table (WebOS Reference)
- **WebOS.Auth** → Header lists identity contexts, token duties, and dependency intent (optional hooks). Footer enumerates five concrete tasks (validate session, require login, cache scope, parse token, issue token) with inline code.
- **WebOS.Boot** → Header compresses purpose/highlights, while footer provides a developer cheat-sheet with “What does Boot prepare?” bullet lists. Reuse this balance for lifecycle scripts.
- **WebOS.Cache** → Header includes Router Map + Router Payload Format plus mode definitions; footer restates the three cache modes, advanced usage, option defaults, and delete patterns.
- **WebOS.Context** → Header doubles as an API index (Purpose + Highlights + Dependency tree); footer reminds engineers about override safety and locking semantics.
- **WebOS.Manifest** → Header explains layered merge strategy + dependency expectations (Aliens constants, logging, cache, DB). Footer showcases tenant/app usage, subset fetch, dot-path retrieval, and reload flow.
- **WebOS.Security** → Header stresses deny-by-default, method/role/rate enforcement, and dependency graph; footer provides boot guard recipe, cache scope rules, and extension hooks. Treat this table as a mandatory inspection list before signing off any enterprise file.

### 5.10 Header Field Matrix (Sample-Driven)
| Required Field | Expectation | Sample Cue |
|----------------|-------------|------------|
| `[HEADER] Name` | Use `WebOS.Auth` style naming with component + capability (e.g., `WebOS.Security`) so audit logs stay searchable. | `[ENGINE] WebOS.Auth` header line |
| `Purpose` | One bullet per responsibility pulled straight from the modules (identity context, session validation, token parsing, signed tokens, etc.). | Purpose bullets inside WebOS.Auth/WebOS.Security |
| `Highlights` | At least three behavior-focused statements (“Deny-by-default security mindset”, “Boot-friendly sequence”, “Mode detector handles callable/variable/key”). | WebOS.Auth, WebOS.Boot, WebOS.Cache highlights |
| `Dependencies` | Grouped rows for Engines, Globals, Helpers, Optional; mention why each dependency exists (“WebOS::Security() — optional IP/user-agent checks”). | WebOS.Auth + WebOS.Security dependency blocks |
| `Router Map` | Explicit facade-to-class mapping for every entry point, matching WebOS.Cache + WebOS.Context. | Router block in WebOS.Cache header |
| `Router Payload Format` | ASCII divider block enumerating payload indexes/keys with comments for options (`ttl`, `allow_guest`, etc.). | Payload block in WebOS.Cache |
| `Status Ledger` | Include `Status`, `Author`, `Maintainer`, `LastAudit`, `Notes` rows just like the Auth/Security files. | Ledger sentences in every attached engine |
| `Version Footprint` | Mandatory block containing `AliensStyle`, `LastModified (UTC ISO8601)`, and the relevant ecosystem version(s): `WebOS` for /WebOS, `WebSDK` for /SDK, `App` for /Apps. Unknown values MUST be written as `N/A`. | Section 5.3a + header examples |

### 5.11 Footer Field Matrix (Usage Encyclopedia)
| Footer Slice | Expectation | Sample Cue |
|--------------|-------------|------------|
| `[CODE FOOTER] Usage Guide` | Provide ≥2 workflows (Boot invocation + alternate context) with copy-paste code that matches the actual public API. | Steps inside WebOS.Auth + WebOS.Security footers |
| `[OPTIONS] / [ADVANCED]` | Enumerate knobs like `allow_guest`, `bind_tenant`, `force_fresh`, TTLs, cache scope hints. | WebOS.Auth options + WebOS.Cache advanced section |
| `Router Recap` | Restate Router Map at the bottom for quick copy-paste, mirroring WebOS.Cache and Manifest. | Footer of WebOS.Cache |
| `Notes` | Mention envelope, cache scope rules, or caveats (“Login required”, “No cache on sensitive endpoints”). | Footer bullets in WebOS.Security |
| `[CHANGE LOG]` | Append a mini ledger for the current change so auditors understand context without reading git history. | Change log statements at the end of WebOS.Auth/WebOS.Cache footers |

Use these matrices while drafting headers/footers; copy the structure from `/WebOS/PHP.26.00.00/Engine/WebOS.Auth.php`, `WebOS.Boot.php`, `WebOS.Cache.php`, `WebOS.Context.php`, `WebOS.Manifest.php`, and `WebOS.Security.php` whenever you need a reference exemplar.

### 5.12 Header/Footer Example Blocks (Do Not Deviate)
- **WebOS.Auth Header**: Recreate the stacked metadata order → `[HEADER] Name`, `Version`, `Author`, `Location`, multiline `Purpose`, multiline `Highlights`, grouped `Dependencies`, final `Notes`. Every API header must follow this order unless a workflow explicitly overrides it.
- **WebOS.Boot Header**: Include `Author`, `Purpose`, bullet `Highlights`, and `Dependencies` right after the ASCII bars. Lifecycle scripts (Boot, Install, Migration) must reuse this block to stay recognizable.
- **WebOS.Cache Router Segment**: After the overview add `ROUTER MAP` and `ROUTER PAYLOAD FORMAT` blocks exactly as shown (all caps title + dashed divider + inline comments for every payload index). Engines exposed via `WebOS::Anything()` must replicate this chunk.
- **WebOS.Security Header**: Document purpose sub-bullets (“Centralized security policy enforcement…”) followed by highlight statements (“Deny-by-default”). Security/guard-style files must list validation, auth, role, and rate-limit responsibilities separately just like this sample.
- **Footer Copy**: Borrow the closing encyclopedia style from WebOS.Cache (three modes, options, delete instructions) and WebOS.Security (boot recipe + extension hooks). At least two numbered examples PLUS one advanced/options block are mandatory.

### 5.13 Header Build Ritual (Auth/Boot Mirror)
1. Drop the copyright line, then open the ASCII bar `/* ============================================================================`.
2. Declare `[HEADER] <Name>` on its own line (e.g., `[ENGINE] WebOS.Auth`) followed by a horizontal `------------------------------------------------------------------------------` divider, mirroring the attached engines.
3. Populate metadata in the exact Auth order: `Version`, `Author`, `Location`, `Engine/File Type`, `Purpose` bullet list (one responsibility per bullet), `Highlights` bullet list (behavior-focused), and `Dependencies` grouped into Engines / Globals / Helpers / Optional.
4. Immediately follow with `Router Map` and `Router Payload Format` subsections whenever the file is callable via `WebOS::Something()`. Copy the layout used by WebOS.Cache—including inline comments for payload indexes—and never skip this block for router-facing engines.
5. Close the header with the audit ledger (`Status`, `Author`, `Maintainer`, `LastAudit`, `Notes`) exactly like WebOS.Auth and WebOS.Security. If multiple roles exist, dedicate one line per role.
6. Finish with `============================================================================ */` before any PHP code. Missing, reordered, or collapsed segments automatically fail reviews.

### 5.14 When Referring to Existing Files (Minimum Evidence)

If you claim “this is how our system does it”, you must show evidence patterns:

1. Provide at least **two** numbered “recipes” that cover the primary usage paths (e.g., *Boot → Auth → Cache*, or *Auth → DB → Response*).
2. Provide at least **one** snippet that matches a *real* existing pattern (for example, how WebOS.Auth logs, validates, and returns).
3. Clearly state the **file scope** (WebOS core vs WebSDK/WebApps/WebBrand) because that decides `x()` vs `d()` logging.
4. If you are uncertain, output **FAILED: Missing Evidence** and list what file(s) must be found or referenced.

### 5.15 Router Payload Field Guide (Cache Mirror)
- When documenting payloads, keep the ALL-CAPS title (`// ROUTER PAYLOAD FORMAT`) plus dashed divider exactly as WebOS.Cache shows.
- List every index/key with inline comments: `0 => $Callable  // 'Func()' | '$Var' | 'Key'`. If options accept arrays, indent and enumerate each flag with its default (`'ttl' => 600`, `'force_fresh' => false`).
- Duplicate the block per entry point if behavior differs (e.g., `Main` vs `Get`). The attached Cache engine repeats semantics for `Main`, `Get`, and `Delete`; AliensStyle expects the same thoroughness.
- Mention related globals or helpers (`Away()`, `Aliens['App']`, `REQUEST()`) right inside the payload block when they influence UID/Scope just like the reference engine.
- Router payload documentation is mandatory for any file callable via `WebOS::X()` (Auth, Cache, Manifest, Security, Context). Missing payload notes disqualify the header.

### 5.16 Dependency Block Layout (Auth/Manifest Mirror)
- Group dependencies exactly as the sample engines do: `Engines`, `Globals/Consts`, `Helpers`, `Optional`, each with a short “why” note (e.g., `WebOS::Security() — optional IP/UserAgent checks`).
- If a dependency is optional, call it out inline the way WebOS.Auth lists `WebOS::Security()` as optional. Never leave the reviewer guessing whether a hook is mandatory.
- When a section references external config (Aliens constants, Manifest layers), name the specific keys (`Aliens['WebOS']['path']`, `MANIFEST['Cache']`). This mirrors WebOS.Boot and WebOS.Cache clarity.
- For SDK APIs, dependencies must list `Access()`, `import()`, `REQUEST()`, and logging helpers exactly like the provided template so scaffolding tools can diff them automatically.

### 5.17 “Strict but Practical” Rule

Be strict on the **contract**, flexible on the **internals**:

- Strict: required files, required functions, naming, headers/footers, logging, response envelope, and security defaults.
- Practical: internal helper choices (as long as they do not break contracts), performance optimizations, and refactors.

Never accept “hand-wavy” guidance. Every rule must be concrete, testable, and directly implementable.

### 5.18 Header/Footer Audit Steps (Before Merge)
1. Compare the new header against WebOS.Auth/WebOS.Cache: Purpose bullets must list every responsibility, Highlights must describe behavior (not marketing), dependencies must be grouped + annotated, and Router Map/Payload blocks must exist when EngineRouter is involved.
2. Ensure footer usage guides list at least two numbered recipes plus the `[OPTIONS]` block, and restate Router Map lines exactly like WebOS.Cache and WebOS.Security footers.
3. Confirm ledger rows (`Status`, `Author`, `Maintainer`, `LastAudit`, `Notes`) appear in the header AND the footer change log mirrors the latest update (Auth/Security precedent).
4. Reject any header/footer that is shorter or less detailed than the attached engines; “thin” blocks automatically fail review regardless of code quality.

### 5.19 Header DNA Scorecard (Auth/Boot/Cache/Security Mirror)
| Header DNA Slice | Non-Negotiable Rule | Sample Reference |
|------------------|--------------------|------------------|
| Prefix + Version | Always start with `[HEADER]/[ENGINE] Name` + `Version : PHP.xx` exactly like WebOS.Auth and WebOS.Boot. No other text precedes this pair. | WebOS.Auth header top lines |
| Purpose Stack | Minimum four bullets spelling out discrete responsibilities (context, guards, cache policy, tokens). Lifecycle scripts mirror Boot’s phase summary. | WebOS.Auth Purpose block |
| Highlights Stack | ≥3 bullets describing behavior (“Deny-by-default”, “Phase ordered boot”, “Multi-mode cache”). Marketing copy is rejected. | WebOS.Security Highlights |
| Dependency Grid | Grouped sections (Engines, Globals, Helpers, Optional) with inline “why” notes. Optional hooks flagged as such. | WebOS.Manifest dependency rows |
| Router Segments | If callable via `WebOS::Something()`, include both `Router Map` + `Router Payload Format` blocks with ASCII dividers identical to WebOS.Cache. | WebOS.Cache router section |
| Audit Ledger | Status, Author, Maintainer, LastAudit, Notes lines close the header. Values must stay current and mirrored in the footer change log. | WebOS.Security ledger |

Run this scorecard before merging any enterprise header. If even one row fails, the header is rejected and must be rewritten to match the cited reference file.

---

## 6) CleanCode Rules (AliensGrade)

### 6.1 Indentation
- 2 spaces only
- No tabs
- Consistent braces

### 6.2 Sections
Use consistent section headers:
- `[CODEHEADER]`
- `[SECTION] INPUTS`
- `[SECTION] GUARDS`
- `[SECTION] NORMALIZE`
- `[SECTION] VALIDATION`
- `[SECTION] PAYLOAD`
- `[SECTION] EXECUTE`
- `[SECTION] RESPONSE`
- `[CODEFOOTER]`

### 6.3 Section Taxonomy (Engines & Large Modules)
- Engines follow a larger vocabulary in addition to the API sections above. Allowed tags:
  - `[ENTRY]` for main entry points.
  - `[PHASE <n>]` for sequential boot phases (InitEnvironment, InitPaths, ...).
  - `[SECTION] <Name>` for reusable blocks (Runtime State, Token Parser, Cache Scope helper, etc.).
  - `[HELPER]`, `[FOOTER]`, `[USAGE GUIDE]` for specialized guidance.
- Always prefix the block with an ASCII divider comment similar to the samples so that readers can collapse sections quickly.
- Inside each block, keep the pattern `// ----- description -----` or bullet lists explaining purpose before the actual code.

### 6.4 Narrative Comments (Micro-Summaries)
- Before every non-trivial code block, describe WHAT + WHY using compact Hinglish sentences, just like the WebOS.Auth/WebOS.Security examples (e.g., `// Supports Bearer + API-Key + query token`).
- Prefer list-style explanations inside multi-line comments when more than one responsibility exists.
- Mention optional integrations explicitly ("optional: WebOS::Security"), so future engineers know which hooks can be wired.

### 6.5 PascalCase Discipline (Internal State)
- Every internal variable/array should be PascalCase (`$UserLogin`, `$Payload`, `$CacheLocal`).
- If legacy request/DB keys are lowercase, keep the payload keys as-is but wrap them via PascalCase variables first:
  - `$UserLogin = REQUEST('user_login') ?? null;`
  - `$Payload['user_login'] = $UserLogin;`
- Avoid short, single-letter variables (`$s`, `$c`) in new code.

### 6.5a Legacy Parameter Names (SDK Tolerance)
- Legacy SDK functions may use parameters like `$Q`, `$Array`, `$UID`, `$Status`, `$SQL`, `$Table`. Preserve as-is in existing files to avoid breaking.
- New class methods and new APIs must use PascalCase parameter names (`$Payload`, `$Filters`, `$Options`, `$UserId`). Thin wrappers may translate legacy param names to PascalCase internally.

### 6.6 Structural White Space
- Separate every major section with a 60+ character divider (either `// ---` or `/* ----- */`).
- Keep blank lines between metadata rows inside headers/footers to mimic the readability of WebOS.Auth and WebOS.Cache.

### 6.7 Small, Deterministic Functions
- One function = one job
- No hidden side effects
- Explicit returns

### 6.8 Class / Engine Idempotency Law

Every engine/class definition must be safely re-includable:

- Wrap the definition with `if( !class_exists('EngineClassName') ) { ... }`
- Never rely on “include order” as correctness.
- This keeps repeated includes idempotent across Boot/Router layers, CLI, Cron, and Webhook entrypoints.

### 6.9 Helper Placement + Sequencing (Cache/Security Mirror)
- Keep helper sections grouped at the bottom under `[SECTION] Internal Helpers` or `[SECTION] Logger`, identical to WebOS.Cache and WebOS.Security. Mixing helpers with core flow is prohibited.
- Order helpers alphabetically or by call frequency (Cache keeps UID/Mode helpers before logger utilities). Document each with a short Hinglish summary before the first line of code.
- When helpers expose public wrappers (e.g., WebOS.Context’s `ContextGet`, `ContextSet`), place them after the main command set so developers see the primary API before the shorthand.
- If a helper wraps optional integrations (RateLimit, SafeLog), note that inside the comment block and call `function_exists()` before use, mirroring the SafeLog and RateLimit helpers from the attached engines.

### 6.10 Class & Section Ordering (Auth/Boot Mirror)
- Classes must declare runtime state first (properties like `$Context`, `$LastError`), followed by public entry points, then private helpers. This mirrors WebOS.Auth’s order (Runtime State → Context → Roles → Tokens → Helpers).
- For static workflows (Boot), keep `[PHASE]` sections in numeric order and place helper utilities (SafeLog, SafeEngineCall) after all phases, exactly like WebOS.Boot.
- API files must follow `[CODEHEADER] → runtime vars/log init → import/module instantiation → [SECTION] INPUTS → GUARDS → PAYLOAD → EXECUTE → RESPONSE → [CODEFOOTER]`. Deviations from this order are rejected.
- Document each section with Hinglish summaries before code (e.g., “// Populate Aliens container with system + request info”) to match the narrative structure of the reference engines.

### 6.11 Divider Bars & Section Captions (WebOS.Cache Mirror)
- Scope: Engines (WebOS.Auth/Cache/Security/Boot), SDK module classes, cron/CLI orchestrators, and any file longer than ~80 lines MUST segment logic with divider blocks. Tiny inline helpers (<10 lines) may skip unless they introduce a new responsibility.
- Format: every major block gets the three-line pattern visible in the WebOS.Cache screenshots:
  1. Line 1 = ≥60 char ASCII bar (e.g., `// ==========================================================` or `/* ------------------------------------------------------------------ */`).
  2. Line 2 = canonical tag + name (`// [ENTRYPOINT] MAIN (WebOS::Cache)`, `// [SECTION] PAYLOAD ENCODE / DECODE`, `[HELPER] SafeLog` etc.).
  3. Line 3 = one-line Hinglish summary describing WHAT/WHY for the upcoming code ( “// Cache MISS ko serialize karke store karna”).
- Placement guidelines:
  - Entry/exit points (Main/Get/Delete), router payload docs, helper clusters (Encode/Decode pairs), and admin utilities (CacheFlushAll) all require dividers.
  - Nested helpers can downgrade to shorter bars (`// ------------------------------------------------------------------`) but must still repeat the tag + summary so editors collapse cleanly.
  - Always insert a blank line after the divider trio before the first statement; this keeps folding boundaries identical across IDEs.
- SDK/APIs generated via workflows must inherit these dividers for `[SECTION] INPUTS`, `[SECTION] GUARDS`, etc., so the codebase stays searchable and matches the reference screenshot styling.

### 6.12 Header Access Helper Standard (Auth/Boot Mirror)
- Never read HTTP headers directly in business logic. Implement and use a `HeaderGet($Name)` helper that:
  - Normalizes common server vars (`HTTP_AUTHORIZATION`, `Authorization`, `X-API-KEY`, etc.).
  - Falls back to `getallheaders()` when available.
  - Returns `null` on missing keys without emitting notices.
- Bearer parsing must accept `Authorization: Bearer <token>` only (case-insensitive on `Bearer`). No other auth schemes are allowed unless explicitly required by legacy behavior.

### 6.13 Base64URL & Crypto Hygiene (Auth Mirror)
- Use RFC 4648 base64url encoding/decoding without padding for token payloads and signatures.
- Keep helpers local to the engine (`Base64UrlEncode`, `Base64UrlDecode`) and do not rely on global polyfills.
- Compare signatures using constant-time comparison (`hash_equals`) or equivalent to prevent timing leaks.
- Token signature algorithm: `HMAC-SHA256(payload, secret)` as used by WebOS.Auth. Do not silently switch algorithms.

### 6.14 Typed Config Accessors (Auth/Boot Mirror)
- Create typed readers like `ConfigInt($Key, $Default)` inside engines that need numeric TTLs or counters.
- Always clamp/validate values after reading from config to avoid degenerate settings (e.g., negative TTL, zero window durations).
- For secrets, prefer `AuthSecret()`-style helpers that read from `Env → WebOS.Config` (in that order) and never from literals.

### 6.15 Non-Static Module API (Account Mirror)
- WebSDK module classes MUST expose instance methods only. Reserve `static` methods for WebOS engines and legacy procedural shims. Instance APIs keep per-request state (`$Context`, `$Filters`) and align with the Account/Withdraw classes that instantiate via `$ModuleObject = new ModuleName();`.
- Do not introduce `public static function Main()` inside module classes; if a dispatcher is needed, add an explicit method (`Dispatch()`) that the API layer calls on the instantiated object.
- Constructors remain lightweight (init logs + dependency capture). Heavy runtime state must be initialised lazily inside the first method call instead of static caches.

### 6.16 Row-Family Method Naming (AliensCRUD Bridge)
- Primary CRUD methods must follow the legacy row naming grid: singular accessor (`Module()`), list accessor (`Modules()`), creator (`ModuleNew()`), updater (`ModuleUpdate()`), and status toggler (`ModuleStatus()`). When the module exposes helper aliases, mirror the pattern (`TeacherRow()`, `TeacherRows()`, `TeacherRowNew()`, etc.).
- Feed all database work through AliensCRUD helpers (`Row()`, `Rows()`, `RowNew()`, `RowUpdate()`, `RowStatus()`). Method names should reflect the helper they rely on so downstream teams know which CRUD path fires without opening the body.
- When adding specialist helpers (attendance sync, approvals), include the module prefix + action (`TeacherAttendanceRows`) and keep parameters ordered like the corresponding AliensCRUD helper (payload, filters, SQL override). This enforces deterministic discovery via `$PHP['Module']['function'][]` manifests.

### 6.17 Section Buffer & Spacing Law
- Maintain two blank lines between major method blocks and at least one blank line before and after every divider trio (`// =====`). This keeps folding boundaries identical to Account/Withdraw so editors and diff tools align cleanly.
- Inside methods, group related steps with single blank lines following the `[SECTION]` taxonomy (Inputs → Guards → Normalize → Execute → Response). Never collapse multiple responsibilities on adjacent lines.
- Align braces and indentation exactly two spaces from the margin and keep inline arrays formatted vertically when they exceed two keys. Spacing drift is treated as a style violation, not a cosmetic nit.

### 6.18 Function Micro-Header Blocks
- Every public method MUST begin with a short doc banner before the divider, capturing at minimum: Developer, Date (or `LastTouched`), Inputs, Outputs, Dependencies, and Logging prefix. Mirror the Account module pattern:
  - `// Developer : AlienCyborg (2025-12-24)`
  - `// Inputs    : $Payload (Teacher data array)`
  - `// Output    : array|null (Row helper result)`
- Private helpers should still declare Inputs/Outputs but may omit Developer once the parent header already states ownership. The point is to allow auditors to understand contract + dependencies without reading the body.
- When methods evolve, update the micro-header (date + highlights) in the same commit so future reviewers can trace behavioral shifts without diff hunting.

---

## 11) Reference Templates

### 11.1 SDK API Template (Standard Envelope)
```php
<?php Access(); ?>
<?php
# Copyright (c) A|iens. All rights reserved.

/* ============================================================================
 [HEADER] <Module>.<Endpoint> (SDK API Endpoint)
 ------------------------------------------------------------------------------
 Version     : PHP.26.00.00
 Location    : /Aliens/WebSDK/PHP.26.00.00/Module/<Module>/api/<Endpoint>.php
 AliensStyle : 26.12.20-AU5d
 LastModified: 2025-12-20T10:35:00Z
 WebOS       : N/A
 WebSDK      : SDK.PHP.26.00.00
 App         : N/A
 Purpose     :
   - Preserve legacy payload shape for inputs (Feature-Loss ZERO)
   - Emit stable AliensStyle response envelope with TraceId
   - Keep downstream module call untouched (thin controller)
   - Provide enterprise-grade logging + audit notes for SDK consumers
 Highlights  :
   - Mirrors legacy guard/order exactly (no contract drift)
   - Uses `$echo` based d() traces (same-line, includes __LINE__ + type)
   - Ships with `[SECTION]` dividers to match WebOS engine readability
 Dependencies:
   - Access()
   - import('<ModuleName>')
   - REQUEST()
   - GMT()
  - d($echo, __LINE__, <type>)
 Router Map  :
   - API Facade : `WebSDK::Api('<Endpoint>')` → `/Module/<Endpoint>.php`
 Status Ledger:
   - Status    : Draft (set to Stable after QA)
   - Author    : AlienCyborg
   - Maintainer: <Team / Squad>
   - LastAudit : <YYYY-MM-DD>
 Notes       : Mention sample URLs + login expectations just like WebOS.Auth
 ============================================================================ */


$TraceId = Aliens['Boot']['RequestId'] ?? bin2hex(random_bytes(8));
$Status = 0;
$Data = null;
$Error = '';
$Meta = array('TraceId' => $TraceId);

$echo = '<Endpoint>() : API init'; d($echo, __LINE__, 'progress');

import('<ModuleName>');
$ModuleObject = new <ModuleClass>();

// ------------------------------------------------------------------
// [SECTION] INPUTS — map REQUEST() to PascalCase vars before reuse
// ------------------------------------------------------------------
$UserLogin = REQUEST('user_login') ?? null;
// ... replicate every legacy key exactly ...

$echo = '<Endpoint>() : Inputs captured'; d($echo, __LINE__, 'progress');

// ------------------------------------------------------------------
// [SECTION] GUARDS — mirror legacy OR/AND ladder (no delete)
// ------------------------------------------------------------------
if (Setting['launched']) {
  if (i['id']) {
    // Original guard logic here
  } else {
    $Error = 'Login First.';
  }
} else {
  $Error = 'This Feature will Be Available Very Soon.';
}

// ------------------------------------------------------------------
// [SECTION] PAYLOAD — build array exactly like the legacy call expects
// ------------------------------------------------------------------
$Payload = array(
  'user_login' => $UserLogin,
  // ...
);

// ------------------------------------------------------------------
// [SECTION] EXECUTE — delegate to downstream module
// ------------------------------------------------------------------
$echo = '<Endpoint>() : Calling <ModuleClass>::Method()'; d($echo, __LINE__, 'progress');
$OutputObject = $ModuleObject->MethodName($Payload);

if ($OutputObject) {
  $Status = 1;
  $Data = $OutputObject;
  $Error = '';
}

// ------------------------------------------------------------------
// [SECTION] RESPONSE — stable AliensStyle envelope
// ------------------------------------------------------------------
$Response = array(
  'Status' => $Status,
  'Data' => $Data,
  'Error' => $Error,
  'Meta' => $Meta
);

echo json_encode($Response);

/* ============================================================================
 [CODE FOOTER] Usage Guide
 ------------------------------------------------------------------------------
 1) HTTP Entry : https://<host>/?api=<Endpoint>&...
 2) Requires   : Access() + logged-in session (i['id']).
 3) Response   : {"Status":0|1,"Data":<output>,"Error":"","Meta":{"TraceId":"..."}}.

 [OPTIONS]
 - allow_guest : false (default) — mention if Access() guards allow guest.
 - throttle    : Document any external rate limit engine if wired.

 [ROUTER MAP]
 WebSDK::Api('<Endpoint>') → /Module/<Module>/api/<Endpoint>.php
 ============================================================================ */
?>

### 11.1a WebSDK Base Listing API (Teachers Reference)

This is the current **Base API** reference for enterprise-grade WebSDK listing endpoints:

- Reference Endpoint: `/Aliens/WebSDK/PHP.26.00.00/Module/Teacher/api/Teachers.php`

Non-negotiable behaviors (copy this pattern to every new list/search API):

1) **Router URL examples (never direct file execution)**
   - Always document examples using:
     - `https://base.software/?api=<ApiName>&...`
   - Never document:
     - `/Aliens/WebSDK/.../Module/<Module>/api/<ApiName>.php?...`

2) **Public message vs internal log separation**
   - `$echo` is internal debug narrative for `d($echo)`.
   - Public response `c` must be safe (use `$Message` or equivalent).
   - Never leak gate/debug into response.

3) **Schema-driven allowlists (security)**
   - `sort_column` must be explicitly allowlisted (do not rely on regex alone).
   - Filters must map to real columns from `/Module/<Module>/sql/index.sql`.

4) **Aggregates in the same API (Enterprise)**
   - Support `method=COUNT` and `method=SUM` when requested.
   - `methodcolumn` rules:
     - COUNT: allow any schema column.
     - SUM  : allow numeric-only columns (explicit allowlist).
   - Aggregates must ignore paging:
     - Force `LIMIT=''` and `OFFSET=''` when `method` is active.

5) **Response envelope (stable)**
   - Always return: `s,t,c,o,paging,ok,code,trace`.
   - For aggregates, `o` should be a scalar value.
```

### 11.2 WebOS Core Logging Template (x)
```php
$echo = 'FunctionName() : Started'; x($echo, __LINE__, 'progress');
```

---

### 11.3 Engine Header Template (WebOS.Auth / WebOS.Cache style)
```php
/* ============================================================================
 [ENGINE] WebOS.Auth
 ------------------------------------------------------------------------------
 Version     : PHP.26.00.00
 Author      : Aliens Company (WebOS)
 Location    : /Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Auth.php
 AliensStyle : 26.12.20-AU5d
 LastModified: 2025-12-20T10:35:00Z
 WebOS       : PHP.26.00.00
 WebSDK      : N/A
 App         : N/A
 Purpose     :
   - Enterprise Auth Brain (multi-app, multi-tenant)
   - Session validation, token parsing, signed token helpers
 Highlights  :
   - Boot-friendly, deny-by-default
   - Cache-ready + optional engines (Security, Log, Cache)
 Dependencies:
   - Engines : WebOS::Cache(), WebOS::Config(), WebOS::Log()
   - Globals : App, i
   - Helpers : GMT(), Away()
 Router Map  : WebOS::Auth() → WebOS_Auth::Main()
 Notes       : Mention optional hooks, feature flags, or TODOs
 ============================================================================ */
```

### 11.4 Engine Footer Template (Usage Encyclopedia)
```php
/* ============================================================================
 [CODE FOOTER] Usage Guide
 ------------------------------------------------------------------------------
 1) Validate session
    $Ok = WebOS::Auth()->ValidateSession(['allow_guest' => false]);
 2) Require login
    if( !WebOS::Auth()->RequireLogin() ){ deny }
 3) Cache scope helper
    $Scope = WebOS::Auth()->Scope();

 [OPTIONS]
 - allow_guest : bool (default false)
 - bind_tenant : bool (token validation)

 [ROUTER MAP]
 WebOS::Auth()          → WebOS_Auth::Context()
 WebOS::Auth('Token')   → WebOS_Auth::ParseTokenFromRequest()
 ============================================================================ */
```

### 11.5 Router Payload Format Template (WebOS.Cache Style)
```
// --------------------------------------------------------------------------
// ROUTER PAYLOAD FORMAT (from WebOS.EngineRouter)
//   $Payload = [
//      0 => $Callable,      // 'Func()' | '$Var' | 'Key' | [Obj,'Method()'] | Closure
//      1 => $Args,          // function args / variable value / key-mode value
//      2 => $Options        // ttl, log_prefix, unit, force_fresh, ...
//   ]
// --------------------------------------------------------------------------
```
- Keep the divider + ALL-CAPS title exactly like the WebOS.Cache sample so tooling can parse it.
- Inline-comment every index/key, even if it “feels obvious”, and mention default units/flags.
- When payload arrays contain nested arrays, indent them to show depth and list accepted option names with examples (`['ttl'=>600,'log_hit'=>true]`).
- If the router payload differs between entry points (e.g., `Main` vs `Get`), duplicate the block per entry so copy/paste engineers never guess.

Use this richer template for any module that behaves like an engine, boot sequence, cache, manifest, or security layer.

### 11.6 Phase + Section Title Template (WebOS.Boot Style)
```
// ==========================================================
// [PHASE 2] InitPaths()
// Resolve root + WebOS path + ecosystem folders
// ==========================================================
```
- Number every sequential step (`[PHASE 1] .. [PHASE 9]`) when the control flow is linear (Boot, Installers, Migration scripts).
- Prefix log statements with the same tag used in the section caption (`$echo = '[Boot] Phase: InitPaths'; d($echo, __LINE__, 'progress');`).
- For non-linear helpers (Cache, Auth, Security), swap `PHASE` with `[SECTION]`, `[ENTRY]`, or `[HELPER]` headers but keep the same ASCII framing.
- Each block must begin with a one-line Hinglish summary describing WHAT + WHY before the first instruction, mirroring the sample Boot comments (“Normalize PHP env for WebOS”).

### 11.7 Access Control Must Be Centralized

Do not scatter permission logic across business code.

- All Access / Permission decisions must be routed through the central Auth/Permission engine.
- Call sites must receive a structured decision payload (Allowed/Denied + reason + trace id).
- If an endpoint cannot be safely evaluated, return **DENY** and include a deterministic reason (no “silent allow”).

### 11.8 Boot Phase Workflow Template (WebOS.Boot Mirror)
```
// ==========================================================
// [PHASE 5] InitConfig()
// Load Global + Env config with merge strategy.
// ==========================================================
private static function InitConfig(): void
{
  $echo = '[Boot] Phase: InitConfig'; d($echo, __LINE__, 'progress');
  // 1) Locate config files
  // 2) Require PHP arrays safely
  // 3) Merge + publish into Aliens['Config']
}
```
- Always number sequential operations and log with `[Boot] Phase:` (or equivalent `[Installer] Phase:`) prefixes. Phases should cover environment, paths, containers, configs, manifests, detection, engines, finalize—no skipping.
- Publish derived data into shared containers (`Aliens['Path']`, `Aliens['System']`, `Aliens['Tenant']`) inside the matching phase just like WebOS.Boot. Do not leak this work into other phases.
- Add helper wrappers such as `SafeLog` and `SafeEngineCall` after the phases to centralize optional integrations, mirroring the Boot reference file.

### 11.9 Usage Guide Encyclopedia Template (Auth/Cache/Security Mirror)
```
/* ============================================================================
 [CODE FOOTER] Usage Guide
 ------------------------------------------------------------------------------
 1) Scenario label #1
   Exact call copied from code (WebOS::Auth()->ValidateSession([...]))
 2) Scenario label #2
   Another concrete example (WebOS::Auth()->IssueSignedToken(...))

 [OPTIONS]
 - allow_guest : Mention default + behavior
 - bind_tenant : Mention scope

 [ROUTER MAP]
 WebOS::Auth() → WebOS_Auth::Main()

 [NOTES]
 - Compatibility, cache scope, envelopes, etc.
 ============================================================================ */
```
- Follow this template for every enterprise footer. At least two numbered scenarios, `[OPTIONS]`, `[ROUTER MAP]`, and `[NOTES]/[CHANGE LOG]` blocks are mandatory.
- Scenario labels must match real-world flows (“Validate session in Boot”, “Signed token issuance”) and the sample code must mirror the actual public API names exactly.
- Options list should capture every flag used inside the file along with defaults (ttl, force_fresh, allow_guest, bind_tenant, etc.).
- Reiterate Router Map lines so a developer can understand entry points without scrolling back to the header.

---

### 11.10 Guard QA Scorecard (WebOS.Security Alignment)
- **Order lock:** Validate Name → Resolve Unit → Validate Method → Require Auth → Enforce Role → Rate Limit → Scope/Cache. Any swap fails review; mirror WebOS.Security exactly.
- **Dual logs:** Log both start (`Security.Guard : Started`) and final decision (`Allowed/Denied`) using `$echo` + helper guard/fallback rules. Missing either log fails Section 3.
- **Decision payload:** Returned array must describe `ok`, `status`, `message`, `scope`, `cache`, `unit`, `name`, mirroring the WebOS.Security contract. Document these keys inside header + footer options.
- **Helper clustering:** Validator helpers (`ValidateName`, `ResolveUnit`, `RequireAuth`, `EnforceRole`, `RateLimit`, `ScopePolicy`, `Error`) live under `[SECTION] Internal Helpers` and follow PascalCase naming.
- **Router parity:** If Guard is exposed through `WebOS::Security()`, restate Router Map + Payload (Section 5.15) and ensure payload docs list guard-specific options (`method`, `allow_guest`, `allow_rate_burst`).

Use this scorecard when building any guard/policy engine—if the behavior or documentation drifts from WebOS.Security, reject the change.

### 11.11 Rate Limit Notation & Keying (Security Mirror)
- Accepted rate grammar: `N/min`, `N/sec`, or `N/<window>s` (e.g., `60/min`, `10/10s`, `5/sec`).
- Parse using a dedicated helper (e.g., `ParseRate()`) and reject invalid patterns deterministically.
- Rate key MUST include identity scope to avoid cross-tenant bleed. Recommended composition:
  - `security:rate:T:<tenant>|A:<app>|U:<user>|N:<name>|M:<method>`
- If a RateLimit engine is unavailable, guard functions must allow the request (soft dependency) and log the skipped enforcement via `$echo`.

## 12) Legacy vs Clean-Room Comparison (TeacherNew)

When TeacherNew.php was “converted” by a generic AI, the output looked pretty but violated AliensStyle. Use these checkpoints before accepting any refactor:

- **Logging Law Drift:** SDK path demands `d($echo, __LINE__, '<type>')` but the AI used `x()` everywhere. Always map logger by path prefix (WebOS vs SDK/Apps/Brand).
- **Dependency Swap:** Legacy endpoint imports `Account` and calls `AccountNew()`. Switching to `Teacher`/`TeacherNew()` is a logic change, not a style change.
- **Validation Inflation:** The legacy OR/AND chain intentionally allows partial payloads. Replacing it with a `$Required` array plus early returns is Feature-Loss.
- **Message/Envelope Changes:** Response shape and error text are contracts. Do not rename/remove `Status/Data/Error/Meta.TraceId`, and do not mutate message semantics without explicit instruction.
- **Guard Flow Mutation:** Injecting extra normalization and return points changes downstream expectations (e.g., numeric casting, new error copy).

If any “AliensStyle” pass introduces the issues above, reject it and re-run with this document + workflow signals.

### 12.1 Enterprise Reference Set (WebOS.Auth/Boot/Cache/Context/Manifest/Security)
- Treat these engines as the gold sample for presentation:
  - Massive `[HEADER]` block describing purpose, highlights, dependencies, router map, and design notes.
  - Rich `[SECTION]` labels for Runtime State, Core Context, Roles + Scopes, Token parsing, Boot Phases, etc.
  - Step-by-step comments before each code segment, explaining intent in plain English.
  - `[CODE FOOTER] Usage Guide` sections with numbered recipes, option tables, and router summary.
  - Generous use of ASCII separators to make 500+ line files still scannable.
- When generating new enterprise code, mirror that depth even if the resulting file is large. Size is not a constraint; clarity and determinism are.

### 12.2 Benchmark Scorecard (Must Match Samples)
| File Type        | Header Expectations | Body Expectations | Footer Expectations |
|------------------|---------------------|-------------------|---------------------|
| WebOS.Auth       | Identity context + token responsibilities, dependencies grouped by optionality | Runtime State → Core Context → Roles/Scopes → Session/Token helpers with narrative comments | 5-step usage flows + sample cache scope formula + signed token recipe |
| WebOS.Boot       | Purpose + highlight stack (env, paths, manifest, detection) | `[PHASE n]` blocks with matching `[Boot] Phase` logs and helper wrappers | Developer cheat sheet enumerating entry flows, preparations, customization knobs |
| WebOS.Cache      | Router Map + Router Payload Format + mode definitions + dependency grid | Dedicated sections for UID engine, mode detector, cache/delete/encode helpers | Three-mode walkthrough + advanced options table + delete patterns + router recap |
| WebOS.Context    | Purpose bullets double as API list; dependency lines mention each resolver | Alphabetized command methods (Get/Set/Has/Pick/Scope/ Snapshot/Restore/Lock) plus helper wrappers | Footer notes on override safety + locking, no example = incomplete |
| WebOS.Manifest   | Header explains layered merge order, optional helpers, status | Body contains `LoadLayer` sections (L0..L5), merge engine, filter + dot-path helper | Footer covers tenant/app fetches, subset retrieval, dot-path usage, reload instructions |
| WebOS.Security   | Header highlights deny-by-default, fast-path validations, cache scope, dependencies | Guard pipeline enumerates ValidateName → Method → Auth → Role → Rate → Scope with helper sections at end | Footer restates API guard snippet, cache scope policy, extension hooks |

Every enterprise file must be scored against this table before delivery. If any cell fails (missing Router Map, phases without matching logs, footer lacking option table), the change is rejected.

## 13) Enterprise Pattern Deep-Dive (Sample Analysis)

### 13.1 WebOS.Auth
- Purpose block covers four sub-systems (identity context, session validation, token parsing, signed tokens). Whenever a file spans multiple responsibilities, list each bullet explicitly under `Purpose` just like this sample.
- Runtime state (`$Context`, `$LastError`) appears before any method definitions. Follow the same order for every class: state → core API → helper methods.
- Token methods articulate each validation step (decode, secret lookup, expiry, tenant/app binding) using inline comments plus logging. Replicate this storytelling whenever cryptography or security logic is present.
- Signed token helpers always pair with `AuthSecret()` + config fallback helpers. Whenever you expose signing logic, include both encode and decode helpers plus config readers inside the same file so consumers never guess where the secret comes from.

### 13.2 WebOS.Boot
- Uses `[PHASE n]` headings for every milestone (environment, paths, config, manifest, detection, engines, finalize). Any script exceeding ~80 lines must adopt the same PHASE numbering to keep logs readable.
- Each phase logs with a consistent prefix (`[Boot] Phase:`). When building other engines, define and reuse a prefix so logs can be filtered quickly (`[Cache]`, `[Security]`, `[Manifest]`, etc.).
- Boot’s header spells out dependencies (`Aliens['WebOS']['path']`, `WebOS::Log()`, etc.) and the footer enumerates debugging/best practices. For any lifecycle entrypoint (Installers, Workers, Migration), clone this header/footer rhythm to ensure ops teams instantly understand prerequisites and observability points.

### 13.3 WebOS.Cache
- Header lists Router Map + payload structure right after the overview. Future engines MUST mention how EngineRouter talks to them (method names, payload indexes, options) near the top of the file.
- Footer enumerates all supported modes (callable, variable, key) plus advanced scenarios and option tables. Treat this as the baseline depth for any reusable helper or utility.
- Body dedicates entire sections to UID generators, argument normalizers, mode detectors, delete engines, and logging helpers—each with their own ASCII divider and Hinglish summary. When you introduce reusable helpers (e.g., Queue, Storage, HTTP clients), isolate the same concerns instead of burying them inside one mega function.

### 13.4 Engine File Layout Contract

Each Engine file must follow this internal structure:

1. CodeHeader (with Author/Requires, Purpose, Highlights, Public API summary).
2. Dependencies / Imports (explicit).
3. `class WebOS_EngineName` definition.
4. `public static function Main($Payload, $Context)` as mandatory fallback.
5. Additional action methods in stable ordering:
   - Prefer **Main first**, then **alphabetical** (Get, Set, New, Update, Delete, List, etc.)
   - Or **lifecycle order** if the engine is clearly lifecycle-driven (Boot → Init → Run → Shutdown).
6. CodeFooter (Usage Guide + examples + troubleshooting).

### 13.5 WebOS.Manifest
- Layer loaders (L0..L5) each live inside clearly named blocks with comments describing when they fire. When building multi-layer processors (docs merge, manifest sync, workflow merge), use the same naming convention.
- Footer reiterates usage for different scopes (tenant/app) and call styles. Document multiple entry points even if they share code paths.
- Merge logic is fully deterministic: `_replace` and `_unset` directives are documented inline, and helper functions (`DeepMerge`, `IsAssoc`, `FilterKeys`) are grouped after the loader sections. Adopt this ordering whenever your engine has both data loaders and data combiners.

### 13.6 WebOS.Security
- Guard pipeline lists each step in order: ValidateName → Resolve Unit → ValidateMethod → RequireAuth → AllowUnit → RateLimit → Scope+Cache. Security-sensitive flows must follow and document the same order, with logs at each stage.
- Helper region sits at the bottom under `[SECTION] INTERNAL HELPERS`. Keep helper utilities grouped at the end to prevent mixing public API and plumbing code.
- Usage block shows three recipes (API guard, cache scope policy, extension hooks). When authoring security or policy engines, always provide at least three situational guides: “happy path”, “cache/rate config”, and “extension strategy”.

### 13.7 Signed Token Contract (WebOS.Auth Mirror)
- Format: `base64url(payload) . '.' . base64url(signature)` where `signature = HMAC_SHA256(payload, secret)`.
- Claims: `iat` (issued-at), `exp` (expiry, required), `nbf` (not-before, optional), plus optional binding claims `tenant_id`, `app_id` when `bind_tenant`/`bind_app` options are enabled.
- Validation order:
  1) Structure check (exactly two parts, both base64url decodable)
  2) JSON payload decode and type validation
  3) Secret retrieval via `Env → WebOS.Config` helper
  4) Constant-time signature compare
  5) Time window checks with small clock skew allowance
  6) Optional tenant/app binding checks
- Failure handling: return normalized errors (`code`, `message`, `hint`) and avoid exceptions for predictable control flow.
- Issuance: default TTLs must be read via typed config accessors; never hardcode in code bodies.

Use these deep dives as a review checklist. If a new file deviates from the traits highlighted in its closest reference (Auth, Boot, Cache, Context, Manifest, Security), reject and regenerate.

## 14) Enterprise Implementation Checklist (WebOS Mirror)
- **Auth / Token Engines:** Start with Runtime State, then context helpers, then token helpers exactly like WebOS.Auth. Include explicit logging echo strings for each validator and mention optional bindings (`bind_tenant`, `bind_app`).
- **Boot / Lifecycle Scripts:** Use `[PHASE n]` blocks with consistent log prefixes (`[Boot] Phase:`) and ensure every phase writes to shared containers (Aliens['Path'], Aliens['System'], etc.) before moving on, just as WebOS.Boot does.
- **Cache / Utility Engines:** Provide Router Map + Router Payload Format blocks, describe all modes (callable/variable/key), and document option flags plus `force_fresh` semantics inside both header and footer (WebOS.Cache blueprint).
- **Context / State Engines:** Offer dispatcher-style `Main()` plus alphabetized sub-commands, Snapshot/Restore helpers, and optional `Lock()` features (WebOS.Context mirror). Explain override safety and multi-tenant awareness upfront.
- **Manifest / Layered Config:** Name each loader layer (L0..L5), describe `_replace`/`_unset` rules, and keep local cache arrays for per-request reuse. Footer must restate tenant/app usage combos (WebOS.Manifest baseline).
- **Security / Guard Pipelines:** Document sequential guard order (ValidateName → Method → Auth → Role → Rate → Scope). Add helper cluster at bottom and show cache scope math plus sample error payloads (WebOS.Security discipline).

Run this checklist before accepting any enterprise file. If any bullet fails, redo the implementation.

### 14.1 Build Ritual (Do This in Order)
1. **Intake**: Read existing file + module manifest + workflow YAML + AliensStyle AU4. Highlight logging law, envelopes, and dependency invariants before touching code.
2. **Header Draft**: Populate copyright, header bars, purpose, highlights, dependency grid, router info, and audit ledger. Do not write code until header passes the Section 5.7/5.9 quality gates.
3. **Body Scaffold**: Lay out `[PHASE]`/`[SECTION]` shells with Hinglish summaries, PascalCase placeholders, and deterministic ordering (state → core API → helpers). Copy the Boot/Auth pattern intentionally.
4. **Implementation**: Fill each section while respecting Feature-Loss ZERO. Logging echoes must match section names, and dependencies must be reused exactly as declared.
5. **Footer Encyclopedia**: Add multi-scenario usage guides, option tables, router recap, and notes. Cross-check with Footer Requirements (Section 5.8) and Scorecard (Section 12.2).

### 14.2 Pre-Flight QA (Before PR / Commit)
- Run the Benchmark Scorecard (Section 12.2) row-by-row.
- Verify all arrays/variables stick to PascalCase.
- Confirm CodeHeader/CodeFooter lengths roughly mirror the reference files (short headers are an automatic fail).
- Ensure every helper referenced in dependencies actually exists in the file or is imported, and optional hooks are clearly labeled.
- Read the file aloud section-by-section: if any part lacks a narrative comment describing WHY, add it.

### 14.3 Enterprise Body Layout Scorecard (Auth/Boot/Security Mirror)
- **Runtime State first**: Classes start with state properties before any public API (`WebOS.Auth` style). APIs begin with runtime vars + `$echo` initialization before `import()` calls.
- **Phase/Section order**: Lifecycle scripts follow `[PHASE n]` numbering identical to WebOS.Boot (InitEnvironment → InitPaths → … FinalizeBoot). Engines use `[SECTION]` labels for logical groupings (Runtime State, Token Parser, Cache Scope, Internal Helpers).
- **Narrative Comments**: Before each block, add a short Hinglish summary describing what the block does (e.g., “// Multi-App detection (host-based rule)”). This mirrors WebOS.Auth/Security readability requirements.
- **Helper clustering**: Keep helpers at the bottom grouped under `[SECTION] Internal Helpers` like WebOS.Security. Do not mix helper functions between core sections.
- **Router-aware entrypoints**: Whenever `Main()` dispatches actions (WebOS.Context/WebOS.Cache), keep alphabetized command ordering and include switch/match comments explaining each branch.

## 15) Change Log
- AU1: Consolidated ecosystem rules + enforced Logging Law (WebOS=x, SDK=d) + Feature-Loss ZERO + CodeHeader/Footer mandatory.
- AU2: Strengthened Feature-Loss ZERO definition and added TeacherNew comparison checklist for quick drift detection.
- AU3: Added enterprise-grade header/footer specs, section taxonomy, engine templates, and WebOS reference set guidance to match the attached sample files.
- AU4: Introduced mandatory Router Payload blocks, audit ledger requirements, header/footer mirror tables, benchmark scorecard, phase template, build ritual, and expanded enterprise checklist.
- AU4b: Added enterprise detail allowance, echo logging reference snippets, concrete header/footer replication guide, and body layout scorecard aligned with WebOS.Auth/Boot/Cache/Security samples.
- AU4c: Locked in logger guard patterns, step-by-step header/footer rituals, router payload field guide, engine class guard requirements, and SDK Access/import sequencing rules to mirror the attached WebOS engines exactly.
- AU4d: Added echo prefix format rules, dependency block layout, footer depth checklist, helper placement sequencing, and new templates for Security guards + Boot phases to enforce parity with the provided WebOS engines.
- AU4e: Expanded echo QA rules, header/footer audit steps, class/section ordering mandates, and usage guide template to eliminate remaining gaps between AliensStyle and the WebOS.Auth/Boot/Cache/Security exemplars.
- AU4f: Introduced echo/helper fallback law, header DNA scorecard, and guard QA checklist so enterprise headers + guard engines stay lockstep with WebOS.Auth/WebOS.Cache/WebOS.Security references.
- AU5a: Added environment baseline (UTF-8, Asia/Kolkata, sane limits, error handlers), Aliens container SoT keys, header access helper, base64url/crypto hygiene, typed config/secret accessors, token header precedence + multi-tenant header rules, normalized error objects, cache scope composition, rate-limit grammar/keying, and signed token contract aligned with WebOS.Auth/Security/Boot.
- AU5b: Mandatory CodeHeader version footprint — add AliensStyle version used, LastModified (UTC ISO8601), and ecosystem versions (WebOS/WebSDK/App) depending on file location; added to header templates and header field matrix.
- AU5c: SDK legacy module rules — allow `Module_Method` naming in `/Module/*/php/index.php`; require `$PHP['<Module>']` manifest with Version Footprint; permit raw SQL and Conditions-DSL (including misspellings) without refactor; define triad signatures for list/update; big-number-as-string policy; zero-date acceptance; table prefix usage; quoted column names; minimal d() logging for legacy; deprecation `_OLD` suffix policy; discourage new fancy Unicode banners.
- AU5d: Aligned with AliensStyle v ChatGPT v10.0.0 (PHP 7.4+ baseline, strict `$echo` same-line logging with `__LINE__` + type, and stable API response envelope with `Meta.TraceId`).
- AU5e: Fixed path naming (`/Aliens/WebApps`, `/Aliens/WebSDK`), clarified logger scope (`x()` WebOS core only, `d()` elsewhere), and removed ambiguous/truncated guidance that confused AI tools.
- AU5f: Added module/engine boundary law, method-level log pairing, branch replay requirements, non-static module API mandate, row-family method naming, spacing buffers, and function micro-header expectations to keep modules aligned with Account/Withdraw exemplars.

