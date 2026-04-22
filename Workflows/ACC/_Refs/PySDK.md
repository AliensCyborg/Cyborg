
# ACC Reference — PySDK

This file is a **reference** for ACC workflows that generate/update PySDK artifacts.
Do not place workflow-specific implementation steps here; keep only canonical PySDK facts.

---

## PySDK Module Contract (Python)
- Entry file: `/Aliens/PySDK/Python.{Version}/Module/{Module}/py/index.py`
- Standard boot order mirrors reference modules (e.g., Account/Withdraw):
	1) `declare(strict_types=1);`
	2) `Access();`
	3) Bootstrap `$echo` + `d()` logs
	4) `import('<Dependency>');`
	5) `$Python['{Module}']` metadata + function signatures
	6) Class `{Module}` with instance methods

## PySDK Module Folder Tree (SSOT)
PySDK me module ka canonical structure:
- `PySDK/Module/{Module}/py/` (primary)
  - `index.py` (class `{Module}`)
- `PySDK/Module/{Module}/sql/` (primary reference)
  - `index.sql` (schema/table family)
- `PySDK/Module/{Module}/api/` (common)
  - `{ApiName}.py` (router-driven API handlers)
- `PySDK/Module/{Module}/cron/` (optional)
- `PySDK/Module/{Module}/webhook/` (optional)

Rare/exception-only (avoid for new work unless explicitly needed):
- `PySDK/Module/{Module}/component/`
- `PySDK/Module/{Module}/css/`
- `PySDK/Module/{Module}/screen/`

## CRUD Primitives (AliensCRUD)
- PySDK modules must call Row-family primitives (engine-owned):
	- `Row($UID, $Table, $SQLData=false, $Array=[])`
	- `Rows($Filter, $Conditions, $SQLData=false)`
	- `RowNew($Data, $Table, $SQLData=true)`
	- `RowUpdate($Data, $Conditions, $Table, $SQLData=false)`
	- `RowStatus($UID, $Status, $Table, $SQLData=false, $Array=[])`

## Database Prefix Law
- Never hardcode full table names with prefix (example: `Coinpods_`).
- Always pass base table name only (example: `teacher_attendance`).
- Row-family applies the runtime prefix internally (`prefix.prefix1.$Table`).

## Naming Rules
- Use **PascalCase** for modules/classes/methods.
- Exceptions: DB column names and request/payload keys.
- Multi-table (cluster) modules must use underscore table family naming with full CRUD:
	- `{Module}_{Table}()`, `{Module}_{Tables}()`, `{Module}_{Table}New()`, `{Module}_{Table}Update()`, `{Module}_{Table}Status()`

## Non-Negotiable: Singular Entity Names
- **Module**, **Class**, and **Table** names MUST be **singular** (never plural).
- Plural forms are allowed ONLY for **list method names** (example: `Teachers()`), not for entity names.
- If a name is ambiguous, default to singular.

## Pluralization Rules (for list methods)
- List method name must be a real English plural (avoid awkward `Xys` patterns).
- Default rules (simple, deterministic):
	- If word ends with consonant + `y` (e.g., `Story`) → replace `y` with `ies` (`Stories`).
	- If word ends with `s`, `x`, `z`, `ch`, `sh` → add `es`.
	- Otherwise → add `s`.


## Logging Rules
- Use `$echo` + `d($echo, __LINE__, 'process|success|error')` heavily.
- Log in each `if/else` branch; avoid silent branches.
- PySDK modules should not add `function_exists()` guards for core runtime primitives.

## AliensStyle Header Rules
- File header (`[HEADER]`) must include `Dependencies` section (Modules/Helpers/Globals).
- Each public method `[CODEHEADER]` must include a `Dependencies` section listing the helpers/engines it calls.

## AliensStyle Section Divider + Code Footer Rules
- Every public method must be preceded by the canonical divider (reference modules: Account/Withdraw):
	- `// ============================================================================`
	- `// [SECTION] {Module}::{Method}()`
	- `// ============================================================================`
- Every module class file must end with a structured footer block:
	- Title: `[CODE FOOTER]` (or `[CODEFOOTER]`)
	- Contains: Usage Guide + Router Map recap + Change Log
	- Last line (canonical divider note):
		- `// Divider: Append future footer notes above this line to keep layout consistent.`

## $Python Manifest Rules
- Every public method must be registered in `$Python['{Module}']['function'][]`.
- Keep signatures aligned with the actual callable surface.

---

## PySDK Module Contract (JS)
- Module JS ka primary location:
	- `/Aliens/PySDK/Python.{Version}/Module/{Module}/js/index.js`
- JS file me typically ek class hoti hai jo export hoti hai.
- Isi file me event listeners / actions / API client helpers ho sakte hain.

Dependency boundary (SSOT):
- PySDK Modules (Python + JS) may use PyOS engines/libraries.
- PySDK Modules (Python + JS) may use other PySDK modules.
- PyOS engines/libraries (Python + JS) must not depend on PySDK modules.

PyApp vs PySDK (SSOT boundary):
- PyApp UI artifacts (Screen/Section/Component/CSS) live inside PyApp theme.
- PyApp ke “business + data access” rules PySDK modules me live karte hain (Class/API/SQL).
- PyApp me custom JS forbidden for new work; JS features module-level JS me aati hain (PySDK Module JS workflows).

