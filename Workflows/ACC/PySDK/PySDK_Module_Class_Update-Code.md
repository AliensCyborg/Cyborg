---
WorkflowId: PySDK_Module_Class_Update-Code
WorkflowName: PySDK Module Class Update Code
Type: Workflow_Singular-Code
ParentWorkflowId: PySDK_Module_Class_Update
Domain: PySDK
Scope: "Module -> Class (Update Code)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Inputs:
  - Targets: "Single target: ModuleName OR ModuleName/FileBase (no extension). Example: User, Web3/Web3_Account"
  - Description: "Human requirement in any language"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/PySDK.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
Quality:
  MustFollow:
    - AliensStyle
    - AliensGrade
Outputs:
  UpdatedFile:
    Location: "/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/index.py"
    SaveStrategy: "modify-only"

Output rule (file-scoped target):
- If target is `ModuleName/FileBase`, primary updated file is:
  - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/{FileBase}.py`
- If target is `ModuleName`, updated files are:
  - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/*.py` (modify-only; no create)
Safety:
  NoDelete: true
  ModifyOnly: true
  MissingTargetIsError: true
  PreserveExisting: true

ExampleFiles:
  - "/Aliens/PySDK/Py.26.00.00/Module/Product/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Student/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Doctor/py/index.py"
  - "/Aliens/PySDK/Py.26.00.00/Module/Book/py/index.py"

Approvals:
  Mode: "From /Aliens/manifest.json"
  ManualIf:
    - "manifest.ApprovalMode == manual"
    - "Any security-sensitive change"
---

# PySDK_Module_Class_Update-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [0] Purpose
Yeh workflow existing module Python classes ko **update** karta hai (modify-only).

Target meaning (NEW SSOT):
- `ModuleName` => update ALL Python class files in module `Python/` folder:
  - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/*.py`
- `ModuleName/FileBase` => update ONLY one file:
  - `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/py/{FileBase}.py`

Hard rule: no auto-create. Agar target file(s) missing: error.

### 0.1 Upgrade Note (Deterministic Result)
This workflow is expected to produce reliable, repeatable output on large legacy files.
When invoked as part of `PySDK_Module_Class_Update`, it MUST execute a planning-derived checklist and must not stop after applying only a small subset of rules.

## [0.1] Hard Gates Checklist (Non-negotiable)
These checks must be satisfied in every run (even when Description is blank):
- Update-only: never create missing target Python file(s) (error instead).
- Header sanity: exactly one `# Python:` at file start; no duplicate open tags; no `` close tag.
- Canonical ordering (index.py only): if the file being updated is `Python/index.py`, ensure `declare(strict_types=1);` then `Access();` at top-of-file when safely possible.
- Non-index files (`_Trait.py`, `{ModuleName}_*.py`, other Python/*.py): do NOT force `Access();` insertion unless the file already follows that pattern; keep changes minimal and non-breaking.
- Secret hygiene: do not add logs that print secrets; if you touch legacy secret-logs, redact them.
- Global AliensStyle: SECTION divider + internal `/* [CODEHEADER] */` for ALL public methods.
- Global Logging: Start + End (Completed/Failed) on every exit path for ALL public methods.
- Single-return rule: each touched public method MUST have exactly one return at the end.
- Return is mandatory; no code allowed after return.
- Global English-only: Translate ALL non-English comments/logs to English.
- Syntax gate: `python -m py_compile` must pass for each updated file.

## [0.2] Execution Mode (ApplyAll Default)
Default behavior:
- **ApplyAll** mode: The workflow MUST apply ALL AliensStyle rules to the ENTIRE file by default.
- Do not stop after one rule.
- Do not ask for confirmation between style fixes unless a critical ambiguity arises.

Override (Guided):
- Only if Description explicitly says "step by step" or "ask me", then use Guided mode.

Output rule (per batch):
- Keep output short: `Done: <BatchName>. python -m py_compile <OK/FAIL>.`

## [0.25] Layered Code Update Model (Non-Negotiable)
This workflow MUST apply rules in ordered layers (especially for large legacy files). Treat the plan as the execution contract.

Layer order (fixed):
1) **Layer 1: Audit-driven update** (execute audit-derived patch steps)
2) **Layer 2: AliensStyle**
  2.1 File Code Header
  2.2 File Code Footer
  2.3 Comment Sections / SECTION dividers
  2.4 Observability: `$echo` + `d()`
  2.5 Function Code Header (Purpose/Input/Output/Dependencies)
3) **Layer 3: Security**
4) **Layer 4: Multi-tenant**
5) **Layer 5: Enterprise-grade**
6) **Layer 6: New ideas** (only safe + aligned with Description)

Hard gates:
- Do NOT start a later layer if earlier layers are FAIL.
- If a layer needs destructive operations (deletions, renames, signature change), STOP and require explicit Description + manifest approval.
- Each layer MUST produce its own mini-report in run artifacts (what changed + gates checked).

## [0.3] Large File Guardrails (Non-Negotiable)
For large legacy module classes (example: 1000+ lines, like W3 ~4787 lines):

Hard rules:
- NEVER rewrite the full file in one pass.
- NEVER truncate/shrink the file.
- NEVER remove/rename existing public methods.
- NEVER remove existing `$Python['{Module}']['function'][]` manifest entries.
- Changes must be applied in multiple small, reviewable batches.

Mandatory artifacts (per run item):
- `Before.index.py` snapshot (full file copy)
- `After.index.py` snapshot (full file copy)
- A short `DiffSummary.md` describing what changed (sections/methods)

Mandatory gates (per batch):
- `python -m py_compile` must pass
- Line-count must NOT decrease unless Description explicitly authorizes deletions
- Method-count must NOT decrease

Additional large-file rules:
- Batch granularity must be small (header batch, footer batch, divider batch, etc.).
- Never claim “success” if Layer 2 remains FAIL after the run; mark as blocked/pending and emit next-step instruction.

## [1] Mandatory Requires (Common Rules)
- Workflow_Universal.md (global rules, logging, output discipline)
- Workflow_Singular.md (singular execution baseline)
- Workflow_Singular-Code.md (code generation + safety rules)
- AliensStyle.md + AliensGrade.md (style + quality gates)
- PySDK/PyOS refs (correct engine/system usage)

## [2] Input Normalization
- Targets: single target expected (`ModuleName` OR `ModuleName/FileBase`)

Targets parsing rules (strict for singular workflow):
- Trim whitespace.
- If empty => `ERR_TARGET_EMPTY`
- If contains comma (`,`) or multiple module names => `ERR_TARGET_INVALID` (use plural parent workflow instead)
- No guessing or auto-splitting inside this workflow.

Target format rules:
- Allowed forms:
  - `ModuleName`
  - `ModuleName/FileBase`
- Only one slash allowed.
- `FileBase` MUST NOT include `.py`.
- `ModuleName` and `FileBase` MUST match: `^[A-Za-z0-9_]+$`
- Reject `..`, dots, backslashes, or additional slashes.

### Module naming validation (strict)
- Allowed: `A-Z a-z 0-9 _`
- Must start with letter.
- No spaces.
- Examples:
  - ✅ `User`
  - ✅ `Account`
  - ❌ `User Profile`

### Singular Entity Name Rule (SSOT) (Non-negotiable)
- ModuleName MUST be singular (never plural).
- ClassName MUST be singular (derived from ModuleName).
- Table names referenced/derived in updated code MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Module Types (SSOT):
- **Singular Module**: exactly 1 required DB table `{ModuleName}`.
- **Plural Module**: multiple required DB tables + units prefixed by `{ModuleName}_` (e.g., `{ModuleName}_Address`, `{ModuleName}_Meta`).

Important:
- Type is decided by required tables/units, not by underscore presence in `ModuleName`.
- Plural units (tables/classes/files/methods) MUST start with `{ModuleName}_`.

---

## [3] Code Update Rules (Strict AliensStyle)
### 3.1 Mandatory layout derived from reference modules
Reference modules (`Account`, `Withdraw`) define the canonical order + constructs. Updater MUST follow this sequence for every class file:
1. `# Python:` + copyright banner + `declare(strict_types=1);` + immediate `Access();` call.
2. Full AliensStyle header block (`[HEADER]` or `[CODEHEADER]`) containing Version/FileVersion/Location/Runtime/AliensStyle/Status/Author/Notes/Purpose/Responsibilities/etc.
3. Bootstrap logging:
  - `$echo = '[Module][Bootstrap] ...'; d($echo, __LINE__, 'process');`
  - All future logs follow `[Module][Method] Message` pattern with `d()` severity tags (`process|success|error`).
4. Required imports + metadata:
  - `import('User');` (and any other module dependencies listed in planning).
  - `$Python['{Module}']['modified']`, `['by']`, and sequential `$Python['{Module}']['function'][N] = 'MethodSignature';` entries summarizing public API.
5. Class definition:
  - Class name == module name (PascalCase), braces on new lines as in references.
  - Provide `__construct()` with deterministic `$echo` logs for init start/end.
6. Method blocks:
  - Each method preceded by `// ============================================================================` section banner.
  - Include inline comment scaffold capturing Developer/Date/Input/Engine/Output exactly like reference files (bullet/numbered steps allowed).
  - Public methods arranged in logical order: lifecycle helpers (bootstrap/sign-in), CRUD (`Module`, `Modules`, `ModuleNew`, `ModuleUpdate`, `ModuleStatus`), followed by domain helpers (e.g., `WithdrawManager`).
  - Preserve legacy signature shapes (`function Method($Q=null)` or `function Method($UID=null)`), no strict typing inside to maintain backward compatibility.
7. Helper usage conventions:
  - Use existing ecosystem helpers (`UID_Encode`, `Row()`, `Rows()`, etc.) rather than inventing new infrastructure.
  - Validation steps expressed via `$echo` logs before DB or helper calls.
  - No exceptions thrown; return arrays/strings exactly as legacy modules do.

Updater MUST refuse to output a class if this ordering or logging contract cannot be honored (raise actionable error pointing to missing planning data instead of emitting malformed file).

### 3.2 Logger Coverage + Finish Pair

- Every generated/updated method must start with `[Module][Method] Started` (`d($echo, __LINE__, 'progress')`) and end with `[Module][Method] Completed` (or `Failed`) using `success`/`error` severity. The workflow must fail if any branch exits without emitting both logs.
- When methods call AliensCRUD helpers (`Row*`, `Rows*`, `UID*`), insert before/after logs describing the payload and result so QA can replay behavior purely from logs.
- Loop-heavy routines (attendance sync, batch status) must suffix the log with iteration context (`: Item #{$Index}`) just like the Account module. This requirement is non-optional and should be validated during diff review.

### 3.3 Non-Static API + Row Naming
- Module classes must remain instance-based; do not generate `static` methods or pseudo-engine dispatchers.
- Primary APIs must follow the canonical CRUD family: `Module()`, `Modules()`, `ModuleNew()`, `ModuleUpdate()`, `ModuleStatus()`.
- If module uses 2+ tables (cluster), secondary table CRUD must use underscore naming and full CRUD family:
  - `Module_Table()`, `Module_Tables()`, `Module_TableNew()`, `Module_TableUpdate()`, `Module_TableStatus()`
- Method names mapped to AliensCRUD helpers must mirror the helper used inside (Row, Rows, RowNew, RowUpdate, RowStatus). Planning output should confirm the CRUD grid; if unknown, stop and request clarification instead of inventing new names.
- Do not create wrapper helpers (`RowHelper/RowsHelper/RowNewHelper/RowUpdateHelper/RowStatusHelper`) inside modules; call Row-family directly.
- PySDK modules must not add `function_exists()` guards for core runtime primitives.
- Database table prefix must never be hardcoded in module defaults/properties/manifests. Always use base table names only because Row-family applies `prefix.prefix1` internally.
- `$Python['{Module}']['function'][]` entries must list every public signature so downstream tooling stays accurate.

English-only + secret safety (Mandatory):
- Developer comments and internal `$echo` log strings must be English-only.
- Never log or hardcode secrets in logs (API keys, private keys, tokens, mnemonics). If a legacy module already contains secrets, the generator must not print them or duplicate them in new logs.

### 3.4 Function Micro-Headers + Spacing
- Prior to each method divider, insert a comment micro-header capturing Developer, LastTouched date, Inputs, Outputs, and Dependencies. Example:
  - `// Developer : AlienCyborg (2025-12-24)`
  - `// Inputs    : $Payload (Teacher data)`
  - `// Output    : array|null`
  - `// Dependencies : Row(), UID_Encode(), PyOS.Auth, PyOS.Validate`
- Function code-header blocks (step-by-step plan/contract) must be inside the function body and must not be merged with outer section dividers.
- Enforce two blank lines between methods and one blank line before/after every divider block. The generator should refuse to emit squashed sections even if the legacy file lacked spacing; auto-formatting is part of the upgrade.
- If an existing file already contains micro-headers, update them to match the new template rather than deleting. Missing metadata is treated as a style failure.
  - ✅ `User`
  - ✅ `Account`
  - ❌ `User Profile`

Module Types (SSOT):
- **Singular Module**: exactly 1 required DB table `{ModuleName}`.
- **Plural Module**: multiple required DB tables + units prefixed by `{ModuleName}_` (e.g., `{ModuleName}_Address`, `{ModuleName}_Meta`).

Description: mandatory; agar blank/missing ho to fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

## [3] Resolve Version + Paths
- LatestVersion default: **latest**
- Fallback: `Py.26.00.00` (agar auto detect possible nahi)
- Resolve:
  - ModuleRoot: `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/`
  - ClassFile: `/py/index.py` (mandatory)

## [4] Preconditions (No guessing)
Hard preconditions:
- Module folder exists
- Class file exists
- `/py/` folder exists (expected structure)
- Read permission ok
- If manifest says manual approval and approval missing:
  - block write operations with `ERR_MANIFEST_APPROVAL_REQUIRED`

## [5] Discover Examples (Mandatory: 4 examples)
Update logic me "examples" ka matlab:
- PySDK ke 4 reference modules/classes dhundo (same ecosystem, similar patterns)
- Prefer:
  1) same domain modules
  2) recently updated modules
  3) cluster vs standard matching

Pick rules (same as generator intent):
- Pick 4 best matches:
  - 2 singular modules (single-table surface)
  - 2 plural modules (modules with `{ModuleName}_*.py` parts and/or multiple `{ModuleName}_*` tables) if available
- Save examples list in RunRecord:
  - `Examples.json` + `Examples.md`
If examples nahi milte:
- proceed with minimal safe patch but add warning `WARN_EXAMPLES_NOT_FOUND`

## [6] Code Update Rules (Unique to this workflow)
Non-negotiable:
- Modify-only: existing file ko patch karna; rewrite nahi
- No deletion, no renames, no moving
- No feature loss: existing public methods ko remove/rename nahi karna
- Keep AliensStyle header/footer intact; missing ho to:
  - add only if safe and does not break runtime (append minimal required header at top, but DO NOT remove existing)

### 6.0A Modular Class Migration (Large-file Prevention) (SSOT)
If planning output includes `PartitionMigrationPlan` (or explicitly lists `Parts[]` + `MoveMethods`), code step MUST apply modularization in a non-destructive way.

**Files involved (all under module Python/):**
- `index.py` (existing; modify-only)
- `_Trait.py` (create-or-update)
- `{ModuleName}_{Part}.py` (create-or-update per part)

Naming rule (NON-NEGOTIABLE): partition classes/files are named **exactly** `{ModuleName}_{Part}`.
- Correct: `W3_Account` (file `W3_Account.py`, import `import('W3/W3_Account')`)
- Forbidden: `W3_AccountPart` / `{ModuleName}_{Part}Part`
- Normalization: if the derived/extracted `Part` token ends with `Part` (example: `AccountPart`), strip the trailing `Part` before generating filenames, classnames, trait names, and constructor property names.

**Hard gates:**
- Do NOT remove/rename any existing public method.
- Do NOT change public method signatures unless Description explicitly requests it.
- `python -m py_compile` must pass after each batch.

**Execution order (must be used):**
1) Ensure part class files exist (create if missing).
2) Ensure `_Trait.py` exists (create if missing) and contains facade traits.
3) Patch `index.py`:
  - Insert `require_once __DIR__ . '/{ModuleName}_{Part}.py';` (for each part)
  - Insert `require_once __DIR__ . '/_Trait.py';`
  - Add `use {ModuleName}_{Part}Facade;` traits to the main class.
  - Add constructor injection + default instantiation of part objects.
4) Migrate method bodies ONLY for methods explicitly listed in planning `MoveMethods`:
  - Copy implementation into the part class.
  - Replace main method body with delegation OR keep wrapper in trait.

**Delegation rule (preferred):**
- Main method remains as-is (name preserved) and returns:
  - `$this->{PartProp}->{Method}(...);`

**Observability/style:**
- New wrapper + new moved method MUST follow AliensStyle + `$echo`/`d()` logs + single-return rule.

### 6.0 Update vs Generate (Do not mix)
- This workflow is **update-only** (modify-only). It must never behave like generator.
- If `index.py` is missing => `ERR_CLASS_FILE_NOT_FOUND` (do not create).
- If `Python/` folder missing => `ERR_MODULE_NOT_FOUND` (or a more specific structure error) (do not create).
- Large rewrites/refactors are forbidden unless Description explicitly authorizes it; otherwise fail with `ERR_REFRACTOR_TOO_LARGE`.

### 6.1 Canonical Module Class Skeleton (MUST match existing pattern)
This update workflow MUST preserve (or safely restore) the canonical PySDK module class ordering as seen in reference modules like:
- `/Aliens/PySDK/Py.26.00.00/Module/Account/py/index.py`
- `/Aliens/PySDK/Py.26.00.00/Module/Withdraw/py/index.py`

**Ordering rules (top-of-file):**
0) Exactly one `# Python:` open tag at file start (no duplicate open tags; no `` close tag).
1) `declare(strict_types=1);`
2) `Access();`
3) AliensStyle header block (comment/docblock)
4) Bootstrap logs (existing `$echo` + `d()` style)
5) `import('...');` dependencies
6) `/** @var mysqli $DB */` + `global $DB;` (ONLY if `$DB` is used in code)
7) `$Python['{Module}']` metadata + `$Python['{Module}']['function'][]` manifest
8) `class {ModuleName} { ... }`

**Forbidden (must not be introduced by workflow):**
- Any executable statements that are clearly debug/test payloads outside the class (e.g., `$Data = [...]`, `$Conditions = [...]`) at top-level.
- Renaming existing `$Python['{Module}']` keys (preserve existing `Created/modified/by` keys; only add missing keys).

Additional enforcement (safe + non-breaking):
- If the module already has `$Python['{Module}']['function'][]` manifest entries:
  - Do not rename/remove them.
  - If you add a new public method (ONLY if Description explicitly requires): add its signature to the manifest.
- PySDK modules must not add `function_exists()` guards for core runtime primitives.

Reference skeleton (illustrative only; do not blindly overwrite legacy code):
```Python
# Python:
declare(strict_types=1);
Access();

/* [HEADER] ... AliensStyle ... */

$echo = '[{Module}][Bootstrap] '; d($echo, __LINE__, 'process');

import('User');

/** @var mysqli $DB */
global $DB;

$Python['{Module}']['modified'] = 'YYYY/MM/DD';
$Python['{Module}']['by'] = 'AlienCyborg';
$Python['{Module}']['function'][] = '__construct()';
// ... every public method signature listed here ...

class {Module} {
  // methods...
}
```
Patch strategy:
- Identify exact insertion points (sections/method boundaries)
- Changes should be minimal and localized
- If large refactor required:
  - fail with `ERR_REFRACTOR_TOO_LARGE` unless Description explicitly allows

### 6.1.1 Logging format alignment (for new/modified logs only)
When you add or edit logs in touched code, align with generator conventions:
- Message pattern: `[{Module}][{Method}] ...`
- Prefer severity tags supported by `d()` where applicable: `process|progress|success|error`.
- Use `d($echo, __LINE__, '{severity}')` if the surrounding ecosystem file uses that form.

For this update workflow, **English-only developer content** is mandatory. If the file contains non-English `$echo` logs/comments, translate them to English in a safe, behavior-preserving way.

Avoid aggressive refactors:
- Do not change runtime logic/returns.
- Do not rename public methods.
- Do not mass-rename variables/array keys unless explicitly requested.

AliensStyle enforcement (Mandatory for WHOLE FILE):
- Add SECTION divider before EVERY public method.
- Add internal `/* [CODEHEADER] */` block inside EVERY public method body.
- Add `$echo` + `d()` entry/branch logs for EVERY public method.

Logging contract (for update checklist execution):
- Each public method in the file must emit:
  - Start log (e.g., `[{Module}][{Method}] Started`)
  - End log (e.g., `Completed` or `Failed`) before every return
- The workflow MUST enforce this systematically across the file to avoid partial compliance.

### 6.1.2 Before/After logs for core helpers (touched code only)
When a touched method calls AliensCRUD helpers (`Row*`, `Rows*`, `RowNew*`, `RowUpdate*`, `RowStatus*`, `UID_*`), add:
- A short pre-call log describing intent (do not dump secrets/PII)
- A short post-call log describing success/failure/shape

Loop-heavy touched routines must suffix logs with iteration context (e.g., `Item #{$Index}`) similar to Account.

DB prefix law enforcement (Mandatory for touched code):
- If any touched method uses hardcoded prefixed table names (e.g., `Coinpods_*`), replace with dynamic `prefix` usage or base-table patterns.

Also forbidden (touched code + touched metadata):
- Do not introduce hardcoded DB prefixes in module defaults/properties/manifests.
- If the module uses Row-family primitives, prefer base table names only (Row-family applies prefix internally).

**Important:** Prefer Row-family primitives where already used by the module.
- If the module uses `Row/Rows/RowNew/RowUpdate/RowStatus`, do NOT refactor to raw SQL.
- If the module already uses raw SQL/prepared statements (like Account), keep that approach but enforce prefix law and safety.

Wrapper helper prohibition:
- Do not create new wrappers like `RowHelper/RowsHelper/RowNewHelper/RowUpdateHelper/RowStatusHelper`.
- Call Row-family helpers directly to keep ecosystem consistent.

CRUD naming rules (do not invent unless required):
- Do not rename existing public methods.
- If (and only if) Description explicitly requires adding missing CRUD surface:
  - Singular module expected family: `Module()`, `Modules()`, `ModuleNew()`, `ModuleUpdate()`, `ModuleStatus()`
  - Plural module expected unit family (pattern):
    - `{ModuleName}_{Part}()`, `{ModuleName}_{Part}s()`, `{ModuleName}_{Part}New()`, `{ModuleName}_{Part}Update()`, `{ModuleName}_{Part}Status()`
  - New methods must be added in a backward-compatible way and registered in `$Python['{Module}']['function'][]`.

English-only enforcement (Mandatory for WHOLE FILE):
- Translate ALL non-English developer comments and `$echo` log strings to English throughout the entire file.

## [6.2] Checklist Execution Contract (Required)
This workflow MUST follow the checklist produced by `PySDK_Module_Class_Update-Planning`.
At minimum, the checklist must cover:
1) CodeHeader + version footprint
2) Canonical skeleton order + bootstrap logs
3) SECTION dividers coverage
4) Internal CODEHEADER coverage
5) Start/finish logs alignment
6) English-only developer content conversion
7) PascalCase for new identifiers only (safe scope)
8) DB prefix law violations removal (if present)
9) `python -m py_compile` after each step/batch

The workflow must apply checklist rules in order, producing a short status output after each step, and honoring Guided vs ApplyAll mode.

Secret hygiene (Mandatory):
- Never add new logs that print secrets (API keys, private keys, tokens, mnemonics).
- If legacy secrets exist, do not duplicate them into new log lines.

### 6.1.3 Non-static API (do not introduce)
- Do not introduce new `static` methods or static dispatch patterns in PySDK module classes.
- Keep module classes instance-based to match ecosystem conventions.

### 6.1.4 Function Micro-Headers + Spacing (Global Enforcement)
- Ensure EVERY public method has a clear SECTION divider and an internal `/* [CODEHEADER] */` block (per AliensStyle rules above).
- Prefer a micro-header near the method divider capturing:
  - Developer
  - LastTouched date
  - Inputs
  - Output
  - Dependencies
- Enforce clean spacing around **all** methods:
  - Two blank lines between methods
  - One blank line before/after divider blocks

Do NOT reformat or re-space the entire legacy file when Description is blank; keep formatting changes localized to the touched method boundaries.

### 6.1.5 Schema awareness (read-only, optional)
If `/Aliens/PySDK/{LatestVersion}/Module/{ModuleName}/sql/index.sql` exists:
- You may read it (read-only) to understand tables/columns.
- Use it to avoid inventing schema and to keep CRUD changes accurate.
If SQL missing:
- Do not invent schema; keep updates schema-agnostic.

### 6.2 mysqli Static-Analysis / Type Hints (Required when touching `$DB`)
When a touched method uses `$DB` (mysqli), add a local PHPDoc type hint adjacent to `global $DB;` in that touched area:
- `/** @var mysqli $DB */`
This prevents analyzer “expected object found null” noise without changing runtime behavior.

Quality gates:
- Naming conventions preserved
- Error handling consistent
- Logging hooks present as per core rules
- Security: input validation + auth checks where relevant (only within described scope)

Return/exception behavior:
- Do not introduce thrown exceptions in touched code.
- Prefer existing ecosystem return styles (arrays/strings/bool) as the legacy modules do.

## [7] Safety + Approval Enforcement
- Auto mode: apply patches directly
- Manual mode: generate an approval request and stop before writing any production-path code

If manual approval required:
- Create: `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`
- Include: target module, planned changes summary, risk assessment, file paths
- Do not write `/Aliens/PySDK/...` until approved
Block conditions:
- Any destructive pattern detected -> `ERR_UNSAFE_OPERATION_BLOCKED`

## [8] RunRecord (Mandatory)
RunRecord must include:
- Target module
- Detected module type (standard/cluster)
- Files touched (only index.py)
- Examples artifacts:
  - `Examples.md`
  - `Examples.json`
- Summary of patches applied (step list)
- Warnings/errors list
- Output paths

RunRecord artifacts parity (if runtime supports per-item artifacts):
- `ResolvedPaths.json`
- `DiffSummary.md` (what changed; include key decisions and why)
- `OutputFiles.json`
- `Status.json` (success/failed/partial + errors)

## [9] Standard Errors (Must be actionable)
- `ERR_TARGET_EMPTY`
- `ERR_TARGET_INVALID`
- `ERR_MODULE_NAME_INVALID`
- `ERR_MODULE_NOT_FOUND`
- `ERR_CLASS_FILE_NOT_FOUND`
- `ERR_MANIFEST_APPROVAL_REQUIRED`
- `ERR_UNSAFE_OPERATION_BLOCKED`
- `ERR_REFRACTOR_TOO_LARGE`
Warnings:
- `WARN_EXAMPLES_NOT_FOUND`
- `WARN_STYLE_MISSING_HEADER`

## [10] Completion Criteria
Success tabhi:
- index.py valid Python syntax (no parse errors)
- Existing features preserved
- Requested changes applied
- RunRecord produced

## [11] Notes for Future
- Future me "Rules()" system jab add hoga, update workflow ko Rules gates bhi follow karne honge (pre-commit quality checks).
