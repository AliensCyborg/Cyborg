---
WorkflowId: WebSDK_Module_Class_Update-Documentation
WorkflowName: WebSDK Module Class Update Documentation
Type: Workflow_Singular-Documentation
ParentWorkflowId: WebSDK_Module_Class_Update
Domain: WebSDK
Scope: "Module -> Class (Docs)"
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
  - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
Outputs:
  DocumentationFile:
    Location: "/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.md"
    SaveStrategy: "create-or-update"

Output rule (file-scoped target):
- If target is `ModuleName/FileBase`, docs MUST be saved as:
  - `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.{FileBase}.md`
Safety:
  NoDelete: true
---

# WebSDK_Module_Class_Update-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

---

## [00B] Documentation Quality SSOT (Non-Negotiable)
This workflow MUST inherit and follow:
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

Meaning:
- Output Roman Hinglish (no Devanagari)
- Long-form, beginner-friendly (3000–8000+ words)
- Mandatory 19-section enterprise structure
- Assumptions + TODO (Team) if anything is missing
- Security/Performance/Observability/Testing sections required

Rule:
- Any local template below is additive coverage; it MUST be mapped into the 19-section structure (must not shorten/override SSOT).

## [01] Required Common Files
- Workflow_Universal.md
- Workflow_Singular.md
- Workflow_Singular-Documentation.md
- Refs: ACC.Targets / ACC.Paths / ACC.Output / ACC.Safety
- Refs: WebSDK / WebOS_Engines / WebOS_System

## [02] Inputs
- Targets: single target item (`ModuleName` OR `ModuleName/FileBase`)
- Description: mandatory (docs emphasis, depth, audience)

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Table names documented/referenced MUST be singular.
- If violated => `ACC_ERR_NAME_MUST_BE_SINGULAR`.

## [03] Target Resolution (Unique Rules)
Accepted targets (NEW SSOT):
- `ModuleName`
- `ModuleName/FileBase`

Validation:
- `ModuleName` and `FileBase` MUST match: `^[A-Za-z0-9_]+$`
- `FileBase` MUST NOT include `.php`
- Only one slash allowed (exactly `ModuleName/FileBase` form)

Source files:
- If target is `ModuleName`:
  - Primary: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/index.php`
  - Secondary (optional, for modular layout completeness): `php/_Trait.php` and `php/{ModuleName}_*.php`
- If target is `ModuleName/FileBase`:
  - Primary: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/{FileBase}.php`

If primary file missing:
- `ERR_CLASS_FILE_NOT_FOUND`

## [04] Documentation Output Path (Unique Rule)
Docs saved to:
- Module target: `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.md`
- File target: `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.{FileBase}.md`

SaveStrategy:
- create-or-update (docs can be created even if previously missing)

## [05] Documentation Template (Must Follow)
Docs file MUST include:
1) Overview (module purpose)
2) Module Type (standard vs cluster, underscore meaning)
3) Folder structure (relevant paths)
4) Class summary (class name, responsibilities)
5) Module bootstrap contract (canonical ordering)
  - `declare(strict_types=1);` + `Access();`
  - header block type (comment/docblock)
  - bootstrap `$echo` + `d()`
  - `import('...')` list
  - `$PHP['{Module}']` metadata + `$PHP['{Module}']['function'][]` manifest expectation
5) Public Methods list:
   - method name
   - params
   - return
   - short purpose
6) Data mapping:
   - tables referenced
   - key columns (if inferable from code/sql)
  - DB prefix law: document base table names; do not recommend hardcoding prefixed names.
7) Security notes (auth/validation assumptions)
  - Secret hygiene: docs/examples must not contain real API keys/private keys/mnemonics.
8) Performance notes (hot paths)
9) Examples (minimum 5 usage examples)
10) Changelog snippet (this update run summary: what changed, date)

---

## [05A] Modular Class Layout (Update Docs Requirement)
When Update run modularizes a module class, documentation MUST include:
- Updated php/ file map:
  - `php/index.php` (main)
  - `php/_Trait.php` (facades)
  - `php/{ModuleName}_{Part}.php` (parts)

File-scoped doc note:
- If the target is `ModuleName/FileBase` and `FileBase != index`, docs MUST focus on that specific file and MUST NOT claim module-wide changes unless the module-wide files were actually updated.
- Migration note (backward compatible): existing calls like `{ModuleName}->{ModuleName}_{Part}New()` still work.
- “What moved” summary:
  - list of migrated method names (from planning `MoveMethods`)
  - list of new/updated part files

Hard rule:
- Do not claim modularization happened unless those files exist in the module.

## [06] Extraction Rules (How docs are generated)
- Primary truth: code in `php/index.php`
- Secondary truth (optional):
  - `/sql/index.sql` if exists
  - related api/cron/webhook folders (if referenced by class)
- Do not invent tables/methods. Agar unsure ho: explicitly mark as "unknown" or skip.

Additional Rule (Consistency):
 - Documentation **Roman Hinglish** me likho (Hindi script / Devanagari allowed nahi).
 - Developer log strings (usually English) ko embed/quote kar sakte ho, lekin unko Hinglish me explain karna mandatory hai.

Additional Rule (Do not normalize legacy metadata):
- If the module uses `Created` vs `modified` keys in `$PHP[...]`, document what exists.
- Do not claim keys exist unless they are present in code.

## [07] RunRecord + Audit
RunRecord must include:
- docs path
- source files used
- sections produced count
- warnings/errors

Deterministic audit artifact (required input):
- Module target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.audit.md`
- File target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.audit.md`
- If missing: mark as `Failed(AuditMissing)` and do not generate docs.

## [08] Errors (Minimum)
- `ERR_TARGET_EMPTY`
- `ERR_MODULE_NOT_FOUND`
- `ERR_CLASS_FILE_NOT_FOUND`
Warnings:
- `WARN_SQL_NOT_FOUND`

## [09] Acceptance Criteria
Success tabhi:
- Docs file exists at the required path
- All template sections present
- Methods list matches current code
- Minimum 5 examples exist

## [10] Examples
1) Standard module docs update:
`Workflow("WebSDK_Module_Class_Update-Documentation", "User", "Docs ko enterprise level banana hai")`

2) Plural module docs update:
`Workflow("WebSDK_Module_Class_Update-Documentation", "User", "Plural module (User + User_Address + User_Meta) tables mapping + methods categorization add karo")`

---

## [11] Update Workflow Upgrades (Docs)
When this workflow runs as a child of `WebSDK_Module_Class_Update`:
- It MUST include the run checklist summary (what rules were applied) as a short changelog snippet.
- It MUST not embed non-English developer log strings in examples.
- It MUST not include secrets in examples (keys/private keys/mnemonics).

Recommended addendum section (short):
- `Last Update RunId` (from RunRecord)
- `Applied Rules` (bullets)
- `Quality` (`php -l` status if available)
