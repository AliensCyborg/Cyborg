---
WorkflowId: WebSDK_Module_Class_Update-Planning
WorkflowName: WebSDK Module Class Update Planning
Type: Workflow_Singular-Planning
ParentWorkflowId: WebSDK_Module_Class_Update
Domain: WebSDK
Scope: "Module -> Class (Update Planning)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Inputs:
  Required:
    - Targets
    - Description
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
Outputs:
  PlanningFile:
    Location: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.md"
    SaveStrategy: "create-or-update"
  AuditFile:
    Location: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.audit.md"
    SaveStrategy: "must-exist (produced by Update-Audit)"

Output rule (file-scoped target):
- If target is `ModuleName/FileBase`, planning MUST be saved as:
  - `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.md`
- And audit input MUST be read from:
  - `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.audit.md`
Safety:
  NoDelete: true
  ModifyOnly: true
  MissingTargetIsError: true
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Student/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Doctor/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Book/php/index.php"
---

# WebSDK_Module_Class_Update-Planning.md

## [00] Purpose
Yeh workflow **sirf planning** generate karta hai, update scenario ke liye:
`Workflow("WebSDK_Module_Class_Update", "User, Account", "...")`

Core goal:
- Existing module class (`php/index.php`) ko read + analyze karke
- Minimal, safe, requirement-focused update plan banana
- Plan ko save karna in developer workspace:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.md`

## [01] Mandatory Includes (Non-negotiable)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

- Workflow-wide rules: `Workflow_Universal.md`
- Singular rules: `Workflow_Singular.md`
- Planning rules: `Workflow_Singular-Planning.md`
- Refs: `ACC.Targets.md`, `ACC.Paths.md`, `ACC.Output.md`, `ACC.Safety.md`
- System refs: `WebSDK.md`, `WebOS_Engines.md`, `WebOS_System.md`

## [02] Input Contract
- Targets: single target item (plural wrapper already split kar chuka hoga)
  - `ModuleName` OR `ModuleName/FileBase`
- Description: mandatory
Rules:
- Agar Description blank/missing ho to fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

## [03] Target Resolution Rules (WebSDK Module)
Resolve module root:
`/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/`

Accepted targets (NEW SSOT):
- `ModuleName`
- `ModuleName/FileBase`

Validation:
- `ModuleName` and `FileBase` MUST match: `^[A-Za-z0-9_]+$`
- `FileBase` MUST NOT include `.php`
- Only one slash allowed (exactly `ModuleName/FileBase` form)

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Table names referenced/discovered MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Required files (MUST exist):
- If target is `ModuleName`:
  - `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/` MUST exist and MUST contain at least one `.php` file.
  - `php/index.php` is still the primary anchor file for module-wide planning.
- If target is `ModuleName/FileBase`:
  - `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/{FileBase}.php` MUST exist.

Missing policy:
- Module missing => `ERR_MODULE_NOT_FOUND`
- Target file missing => `ERR_CLASS_FILE_NOT_FOUND`

## [04] Module Type Detection (Singular vs Plural) (SSOT)
Definitions:
- `SingularModule`: exactly 1 required DB table `{ModuleName}`.
- `PluralModule`: multiple required DB tables + units prefixed by `{ModuleName}_` (e.g., `{ModuleName}_Address`, `{ModuleName}_Meta`).

Detection (preferred evidence order):
1) From deterministic audit artifact `Class.Update.audit.md` (if it includes ModuleType).
2) From existing class references + method-prefix discovery:
  - If any public method matches `^{ModuleName}_[A-Za-z0-9]+` => `PluralModule`
3) From module SQL if present (`/sql/index.sql`):
  - Any table matching `^{ModuleName}_[A-Za-z0-9]+$` => `PluralModule`
Else => `SingularModule`

Planning MUST explicitly mention detected type and implications.

## [05] Table Discovery Strategy (Preferred Order)
Update planning me table discovery ka role:
- Existing class me jo tables already referenced hain, unko primary truth mana jayega.
- Agar module `PluralModule` ho to expected tables:
  - base `{ModuleName}` table + one or more `{ModuleName}_*` part tables (confirmed via class references and/or SQL)
Preferred order:
1) Existing class references (hard evidence)
2) Module SQL file if present: `/sql/index.sql`
3) DB introspection (allowed only if manifest + environment allow; else skip with warning)
No step should cause schema changes in planning stage.

### 5.1 Hard Gate: Base table is mandatory (SSOT)
Rule (non-negotiable): every module MUST have its base table named exactly `{ModuleName}`.

If detected `ModuleType = PluralModule`:
- Planning MUST verify the base `{ModuleName}` table exists in discovered evidence (class references and/or SQL).
- If part unit evidence exists (`{ModuleName}_*`) but base `{ModuleName}` is missing:
  - Mark plan as blocked with deterministic blocker: `BLOCKER_BASE_TABLE_MISSING`.
  - Include fix instruction: “Add/restore base table `{ModuleName}` via `WebSDK_Module_SQL_Update` before applying code updates.”

## [06] Existing Class Analysis (Index.php)
Planning MUST do:
- If target is `ModuleName`: read and analyze the full `php/index.php` first, then enumerate other php/*.php files for scope.
- If target is `ModuleName/FileBase`: read and analyze only `/php/{FileBase}.php` (plus `php/index.php` only if required for context; do not expand scope).

MUST do:
- Read full target file(s) (as per target meaning)
- Identify:
  - class name
  - public API methods (CRUD, business logic)
  - dependencies (Engines/System calls)
  - security assumptions (auth, validation)
  - performance risks (N+1, missing indexes hints, heavy loops)
  - logging/observability hooks
- Extract style constraints:
  - AliensStyle header/footer presence
  - naming conventions
  - non-removal rule (no feature loss)

### 6.1 Canonical WebSDK Module Skeleton Checks (based on reference modules)
Planning MUST explicitly validate whether the file follows the canonical module-class structure (Account/Withdraw pattern):
1) `declare(strict_types=1);`
2) `Access();`
3) AliensStyle header/comment block present (any legacy header is allowed; do not require rewriting it)
4) Bootstrap log lines using `$echo` + `d()`
5) `import('...')` dependencies present
6) `$PHP['{Module}']` metadata + `$PHP['{Module}']['function'][]` manifest present (top-level)
7) Exactly one primary `class {ModuleName}` defined in `php/index.php` for the module.
  - If the module is `PluralModule`, part classes (`{ModuleName}_{Part}`) MUST live in separate files under `php/` and be loaded via `require_once` in `index.php` (not defined inline inside `index.php`).

If any of the above is missing, plan should propose the **smallest safe patch** (insert-only, no rewrite), and note exact insertion points.

Planning MUST also detect and explicitly call out any top-level executable debug payloads outside the class (e.g., `$Data = [...]`, `$Conditions = [...]`).
Update workflow should not introduce these; if they already exist, plan must not delete them automatically (ModifyOnly + non-destructive) but should warn.

Mandatory detections (Update quality gate):
- DB prefix violations: any hardcoded prefixed table names (example: `Coinpods_*`).
- Non-English developer content: Hinglish/Hindi in developer comments or `$echo` strings.
- Secret exposure risks: API keys/private keys/mnemonics present in code or logs.

Precondition (hard):
- Deterministic audit artifact MUST exist before planning:
  - Module target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.audit.md`
  - File target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.audit.md`
  - If missing => fail with `Failed(AuditMissing)`

Planning output MUST include a safe delta plan to fix the above ONLY in touched sections/methods, unless Description explicitly requests a broader refactor.

## [07A] Layered Update Model (Non-Negotiable)
For large legacy files, rules MUST be applied in ordered layers. Planning MUST output a "Layered Checklist" that the Code step can execute deterministically.

Layers (order fixed):
1) **Layer 1: Audit-driven update**
  - Convert audit findings into deterministic patch steps.
  - Must list exact insertion points and non-destructive constraints.
2) **Layer 2: AliensStyle**
  2.1 File Code Header (enterprise header + metadata)
  2.2 File Code Footer (usage + troubleshooting + version notes + hooks)
  2.3 Comment Sections / SECTION dividers (per method)
  2.4 Observability ($echo + d())
  2.5 Function Code Header (Purpose/Input/Output/Dependencies inside each method)
3) **Layer 3: Security** (input validation, auth assumptions, secret hygiene)
4) **Layer 4: Multi-tenant** (no hardcoded tenant assumptions; safe defaults)
5) **Layer 5: Enterprise-grade** (contracts, error envelope, observability, perf notes)
6) **Layer 6: New ideas** (only if safe + aligned with Description; never regress behavior)

Hard rule:
- A later layer MUST NOT start until all earlier layers are either:
  - marked PASS, or
  - explicitly waived by Description + approval (when required by manifest).

Planning output MUST include:
- `LayerStatus` table (PASS/FAIL/TODO) derived from Audit artifact + code scan.
- `LayerGates` section describing what blocks the next layer.
- `Batches` section (how many passes, and what each pass changes) for large files.

## [07] Update Plan Checklist (Unique to Update Workflow)
Plan MUST be expressed as "delta steps" (what to change, where, why):
- Step-wise patch plan (Find -> Insert/Replace -> Expected outcome)
- No full rewrite proposal unless Description explicitly says "rewrite allowed"
Typical update buckets (only if asked / clearly needed):
- Security hardening
- Input validation normalization
- Method extraction / modularization
- Performance improvements
- Better error codes + messages
- Add missing docs blocks / usage blocks

---

## [07B] Partition / Modularization Migration Plan (SSOT)
When `Class.Update.audit.md` reports `PartitionRecommendation=RECOMMENDED` OR Description requests modularization (keywords: `split|modular|partition|tukdo|facade`), planner MUST output a deterministic migration plan.

### B.1 File map (must be explicit)
Planner MUST list exact target files:
- Main: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/index.php`
- Traits: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/_Trait.php`
- Part files: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/{ModuleName}_{Part}.php`

Naming rule (NON-NEGOTIABLE): the partition class/file name is **exactly** `{ModuleName}_{Part}`.
- Correct: `W3_Account` (file `W3_Account.php`, import `import('W3/W3_Account')`)
- Forbidden: `W3_AccountPart` / `{ModuleName}_{Part}Part`
- Normalization: if the extracted `Part` token ends with `Part` (example: `AccountPart`), planner MUST strip the trailing `Part` before building `{ModuleName}_{Part}`.

### B.2 Backward compatibility (non-negotiable)
- Do NOT remove or rename any existing public method.
- Do NOT change how callers instantiate the main module object.
- Preserve method names so existing APIs calling `User->User_AddressNew()` continue working.

### B.3 What moves where (deterministic list)
Planner MUST provide:
- `Parts[]`
- `MoveMethods[{Part}][]`: the exact method names to migrate.
- `KeepInMain[]`: methods that must remain in main class.

Migration rule:
- For each moved method:
  - Create/ensure part class method exists in `{ModuleName}_{Part}`.
  - In main class: replace method body with a thin delegate wrapper OR implement wrapper in facade trait.

### B.4 Require/use wiring steps (must be in checklist)
Planner MUST include steps to:
- Create `_Trait.php` if missing.
- Add `require_once __DIR__ . '/{ModuleName}_{Part}.php';` lines.
- Add `require_once __DIR__ . '/_Trait.php';`.
- Add `use {ModuleName}_{Part}Facade;` in main class.
- Add constructor injection + default instantiation for each part.

### 7.1 Mandatory File-wide Checklist (Always)
This workflow MUST output a checklist that the Code workflow can execute deterministically, in this order (unless Description overrides ordering):

1) **CodeHeader + Version Footprint**
  - Ensure `# Copyright (c) A|iens. All rights reserved.` exists at top (line 2) for PHP files.
  - Ensure header block includes: `AliensStyle`, `LastModified (UTC Z)`, `WebSDK`, `WebOS`, `App`.

2) **Canonical Skeleton Order**
  - Validate: `declare(strict_types=1);` then `Access();`.
  - Validate: bootstrap logs exist (`[{Module}][Bootstrap]`).

3) **SECTION Dividers Coverage**
  - Every public method must have a SECTION divider directly above its signature.

4) **Internal CODEHEADER Coverage**
  - Every method body must include an internal `[CODEHEADER]` block (immediately after start log).

5) **Single-Return Rule (Mandatory)**
  - Every touched method MUST use exactly one return at the end.
  - Return is mandatory; no code after return.

6) **Start/Finish Logs**
  - Ensure `Started` + `Completed`/`Failed` logs exist on every exit path for methods being updated.
  - (If Description requests "full file pass", then enforce across all public methods.)

7) **English-only Developer Content**
  - Translate non-English `$echo` strings + developer comments to English-only.
  - No Hindi/Hinglish inside observability logs.

8) **PascalCase Rule (Safe Scope)**
  - Apply PascalCase to NEW identifiers introduced by this workflow.
  - Do not mass-rename legacy variables or array keys unless Description explicitly requests it.

9) **DB Prefix Law**
  - Detect and list any hardcoded prefixed table names and propose safe replacements.

10) **Syntax/Lint Gate**
  - Plan MUST include a `php -l` verification step after each patch batch.

11) **Function Code Header Dependencies (Mandatory)**
  - Every touched method's code-header MUST include Dependencies (engines/modules/helpers).

### 7.2 Execution UX (ApplyAll Default)
Planning output MUST decide the execution mode:
- Default: **ApplyAll** (Apply all AliensStyle rules to the entire file).
- Guided: Only if Description explicitly requests "step by step" or "ask me".

Planning output MUST include:
- `Mode: ApplyAll | Guided`
- `NextStepPrompt` template (one-liner)

## [08] Output Plan Format (Per Target)
Plan MUST contain:
- Target summary (ModuleName, Standard/Cluster)
- Existing file snapshot metadata (path, size, last modified if available)
- Proposed changes list (numbered steps)
- Risk notes (if any)
- Test checklist (minimum functional checks)
- Done definition (acceptance)

## [09] Error & Warning Codes (for this workflow)
Errors:
- `ERR_TARGET_EMPTY`
- `ERR_MODULE_NOT_FOUND`
- `ERR_CLASS_FILE_NOT_FOUND`
- `ERR_READ_FAILED`
Warnings:
- `WARN_SQL_NOT_FOUND`
- `WARN_DB_INTROSPECTION_SKIPPED`
- `WARN_STYLE_MISSING_HEADER`

## [10] Human Instruction Notes (Premium Quality)
- Plan me clear batana: "exactly kaha change hoga" (file path + section)
- Har step me reason + expected impact (security/perf/maintainability)
- Jo bola gaya hai, **sirf utna**. Extra scope add nahi karna.

## [11] Final Output Rule
Planning output me:
- Only plan (no code)
- Only required tasks
- No speculative features, no unrelated improvements
