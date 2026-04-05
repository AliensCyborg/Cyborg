---
WorkflowId: WebSDK_Module_Class-Documentation
WorkflowType: Workflow_Singular-Documentation
Domain: WebSDK
Category: Module
SubCategory: Class
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-24
Inputs:
  - Targets
  - Description
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
Output:
  DocsRoot: "/Aliens/Docs/WebSDK"
  SaveStrategy: "create-or-update"
Safety:
  NoDelete: true
  ApprovalRequired: false
---

# WebSDK_Module_Class-Documentation
**Purpose (Hinglish):**  
Yeh workflow, WebSDK ke kisi Module (e.g., `User`, `Account`) ki **main class** (usually `php/index.php`) ki **documentation** generate karta hai — targets aur description ke hisaab se — aur docs ko `/Aliens/Docs/WebSDK/` me save karta hai.

> Note: Yeh workflow *sirf docs* banata hai. Code generate/modify ka kaam `WebSDK_Module_Class-Code` me hota hai.

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

---

## [01] Required Common Files
Is workflow ko run karne se pehle yeh dono common rule files apply hongi:

- `Workflows/ACC/_Common/Workflow_Singular.md`
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

In common files me:
- Targets parsing (comma + slash)
- RunRecord + audit
- Safety + no-delete policy
- Standard error model
- Output contract
- Context building (developer manifest, paths, version, theme, etc.)

---

## [02] Inputs
### Targets (Required)
Format examples:
- `User`
- `Account`
- `User, Account`
- `W3/W3_Token_Wallet` (Part Create docs)

Note:
- Module type (Singular vs Plural) is NOT encoded in the ModuleName.
- Plural modules are detected via required tables/units prefixed by `{ModuleName}_` (e.g., `User_Address`, `User_Meta`).

**Meaning:**
- `User` = Singular module when only required table is `User` (primary entry class `php/index.php`).
- `User` = Plural module when required tables/units include `User_*` (e.g., `User_Address`, `User_Meta`). In that case, modular layout (parts + facade traits) is REQUIRED to keep units separated.

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- Table names documented for the module MUST be singular.
- If violated => `ACC_ERR_NAME_MUST_BE_SINGULAR`.

### Description (Required)
Human language me requirements:
- “Is class ko explain karo, methods list banao, examples do, security notes add karo”
- “Cluster tables mapping clearly mention karna”

---

## [03] Target Resolution (Unique Rules)
For each Target item (e.g., `User`):

### Step A: Resolve module location
Expected location (latest version preferred):
- `/Aliens/WebSDK/PHP.26.00.00/Module/{ModuleName}/`

If auto-latest decide possible nahi:
- Default fallback: `PHP.26.00.00`

### Step B: Resolve main class file
Primary class file (95% cases):
- `/Aliens/WebSDK/PHP.26.00.00/Module/{ModuleName}/php/index.php`

If missing:
- **Error** (Modify-only rule): “Module class not found; cannot generate docs.”

### Step C: Resolve related structures (optional but preferred)
If present, use these for docs accuracy:
- SQL structure: `/Aliens/WebSDK/PHP.26.00.00/Module/{ModuleName}/sql/index.sql`
- API folder: `/Aliens/WebSDK/PHP.26.00.00/Module/{ModuleName}/api/`
- Cron folder: `/Aliens/WebSDK/PHP.26.00.00/Module/{ModuleName}/cron/`
- Webhook folder: `/Aliens/WebSDK/PHP.26.00.00/Module/{ModuleName}/webhook/`

---

## [04] Documentation Output Path (Unique Rule)
Generated docs MUST be saved under:

- `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.md`

If file-scope target is used (`Module/FileBase`):
- `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.{FileBase}.md`

Optional extra split (if future me chahiye):
- `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class/{Topic}.md`

But current default: **single `Class.md`** per module.

---

## [05] Documentation Template (Must Follow)
Docs file (`Class.md`) me yeh sections mandatory hain:

1) **Overview**
   - Module ka purpose
   - Standard vs Cluster (`_`) behavior
2) **File Locations**
   - Class file path
   - Related folders (api/cron/webhook/sql)
3) **Public Methods Index**
   - MethodName + short purpose
   - Inputs/outputs summary
4) **Data Model**
   - Standard: main table
   - Cluster: tables list pattern (`{ModuleName}_*`)
   - Primary keys / status columns (if detectable)
   - Table naming note: docs MUST prefer **base table names** (no hardcoded prefix). If code/SQL contains prefixed names, document base name and mention that runtime prefix is applied by Row-family.
   - DB prefix law reminder: never recommend hardcoding `Coinpods_*` (or any prefixed) table in code examples.
5) **How To Use (Examples)**
   - Realistic calling examples (pseudo + your ecosystem patterns)
6) **Security & Validation Notes**
   - Auth checks, permission checks, input sanitization notes
   - Secret hygiene: docs/examples must not include real API keys/private keys/mnemonics.

Additional Rule (Consistency):
 - Codebase me developer comments/log strings ka rule English-only ho sakta hai; documentation ko **Roman Hinglish** me likho.
 - Logs/code strings ko “as-is” quote kar sakte ho (usually English), lekin unko simple Hinglish me explain karna mandatory hai.
7) **Edge Cases**
   - Null/empty inputs, missing rows, pagination, large datasets
8) **Change Log**
   - docs generation timestamp
   - source files referenced

---

## [05A] Modular Class Layout (Docs Requirement)
If the module uses modular layout (parts + facade traits), documentation MUST include:

1) **File Map (php/)**
   - `php/index.php` (main class entrypoint)
   - `php/_Trait.php` (facade traits)
   - `php/{ModuleName}_{Part}.php` (partition classes)

2) **Why this exists**
   - Large file problem (readability + AI planning) and how partitioning fixes it.

3) **How delegation works (backward compatible)**
   - Example: `$Obj = new {ModuleName}();` then `$Obj->{ModuleName}_{Part}New()` works because:
     - main class uses facade trait wrappers
     - wrappers delegate to part-class instances

4) **How to add a new partition**
   - Create `{ModuleName}_{Part}.php`
   - Add trait `{ModuleName}_{Part}Facade` in `_Trait.php`
   - Wire constructor injection + default init in `index.php`

Docs MUST keep examples consistent with DB prefix law and secret hygiene.

Quality requirement:
- Examples zyada hon
- Clear headings, no ambiguity
- “Premium” standard documentation (enterprise level)

---

## [06] Extraction Rules (How docs are generated)
This workflow SHOULD attempt:

- Parse class name (if present)
- Parse public function names (pattern-based)
- Detect dependencies (includes/requires, engine calls)
- Detect table usage (SQL strings / known CRUD calls / table constants)
- Detect configuration hooks (if any)

If code is highly dynamic and extraction uncertain:
- Documentation me clearly mark:
  - “Detected / Inferred” vs “Confirmed”

---

## [07] RunRecord + Audit
Per target item, write:
- Run item logs under: `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`
- Summary should include:
  - Docs file path
  - Source class file path
  - Whether SQL/index.sql was found
  - Warnings (e.g., “unable to parse method signatures”)

---

## [08] Errors (Minimum)
Standard error model is defined in `ACC.Errors.md`. This workflow should raise:
- `ERR_TARGET_INVALID`
- `ERR_MODULE_NOT_FOUND`
- `ERR_CLASS_FILE_MISSING`
- `ERR_DOC_SAVE_FAILED`
- `ERR_PERMISSION_DENIED` *(if docs folder not writable)*

---

## [09] Acceptance Criteria
A workflow run is “Success” only if:
- Docs file exists at the required path
- Docs includes all mandatory sections
- RunSummary is produced

---

## [10] Examples
### Example 1
`Workflow("WebSDK_Module_Class-Documentation", "User", "Explain class, methods list, 5 usage examples")`

### Example 2
`Workflow("WebSDK_Module_Class-Documentation", "User, Account", "Plural (multi-table) mapping clearly mention karna")`
