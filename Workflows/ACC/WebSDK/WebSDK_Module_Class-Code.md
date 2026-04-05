---
WorkflowId: WebSDK_Module_Class-Code
WorkflowName: WebSDK Module Class Generator (Code)
Type: Workflow_Singular-Code
ParentWorkflow: WebSDK_Module_Class
Domain: WebSDK
Engine: ACC.Workflow
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-02
Inputs:
  - Targets: "Module list (comma-separated). Example: User, Account, Web3"
  - Description: "Human requirement in any language"
Outputs:
  - Code: "/Aliens/WebSDK/PHP.{Latest}/Module/{Module}/php/index.php"
  - RunRecord: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
Quality:
  MustFollow:
    - AliensStyle
    - AliensGrade
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"
  ProdWrite: true
  DbTouch: "allowed-but-not-required"
Approvals:
  Mode: "From /Aliens/manifest.json"
  ManualIf:
    - "ProdWrite && manifest.ApprovalMode == manual"
    - "Any security-sensitive change"
ExampleFiles:
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Product/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Student/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Doctor/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Book/php/index.php"
---

# WebSDK_Module_Class-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [0] Purpose
Is workflow ka kaam: **WebSDK Module ke liye class file (index.php) generate/update** karna.

- Target example:
  - `Workflow("WebSDK_Module_Class", "User, Account", "CRUD + business logic clean and consistent")`
- Output example:
  - `/Aliens/WebSDK/PHP.26.00.00/Module/User/php/index.php`
  - `/Aliens/WebSDK/PHP.26.00.00/Module/Account/php/index.php`

> Yeh workflow **sirf Code** karta hai. Planning aur Documentation alag workflows me hoti hai.

---

## [1] Mandatory Requires (Common Rules)
Is file me koi global/common rule define nahi karna.
**Common rules yahan se aayenge:**
- `Workflows/ACC/_Common/Workflow_Singular.md`
- `Workflows/ACC/_Common/Workflow_Singular-Code.md`

Is workflow me unique rules **sirf WebSDK Module Class** ke context ke liye rahenge.

---

## [2] Input Normalization
### Targets (module list)
- `Targets` comma-separated hota hai.
- Har item ko trim karo.
- Empty ignore.
- Duplicate allowed (agar user ne intentionally repeat kara ho).

**File-scope (Part Create):**
- `Module/FileBase` allowed (example: `W3/W3_Token_Wallet`)
- Means create `/php/{FileBase}.php` and wire into `php/index.php`.

### Pluralization (non-negotiable)
- List methods ke liye **proper English plural** use karna mandatory hai.
- Naive `+s` wali approach se awkward words (e.g., `Storys`) ban jate hain — yeh allowed nahi.
- Deterministic rules (same as WebSDK reference):
  - Consonant + `y` → `ies` (Story → Stories)
  - Ends with `s/x/z/ch/sh` → `es`
  - Else → `s`

### Singular Entity Name Rule (SSOT) (Non-negotiable)
- ModuleName MUST be singular (never plural).
- ClassName MUST be singular (derived from ModuleName).
- Table names used/derived in generated code MUST be singular.
- If violated => fail with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

### Module naming validation (strict)
- Allowed: `A-Z a-z 0-9 _`
- Must start with letter.
- No spaces.
- Example:
  - ✅ `User`
  - ✅ `Account`
  - ❌ `User Profile`

Module Types (SSOT):
- **Singular Module**: exactly 1 required DB table `{ModuleName}`.
- **Plural Module**: multiple required DB tables + units prefixed by `{ModuleName}_` (e.g., `User_Address`, `User_Meta`).

Important:
- Module type is decided by tables/units, not by underscore presence in `ModuleName`.
- Plural units (tables/classes/files/methods) MUST start with `{ModuleName}_`.

---

## [3] Resolve Version + Paths
### Version
- Default: **latest**
- Fallback: `PHP.26.00.00` (only if auto-detect not possible)

### Paths (must match Aliens ecosystem)
- WebSDK root:
  - `/Aliens/WebSDK/PHP.{Version}/Module/`

For each module `{M}`:
- Module root:
  - `/Aliens/WebSDK/PHP.{Version}/Module/{M}/`
- Class file output (main):
  - `/Aliens/WebSDK/PHP.{Version}/Module/{M}/php/index.php`

For file-scope `{M}/{FileBase}`:
- Part file output:
  - `/Aliens/WebSDK/PHP.{Version}/Module/{M}/php/{FileBase}.php`
- Wire-up required in `php/index.php` (require + constructor wiring).

---

## [4] Preconditions (No guessing)
This is **Module_Class** workflow, not Module creator.

So:
- If `/Module/{M}/` missing => **error**
  - Guidance in error: “Run `WebSDK_Module` or `WebSDK_Module_Structure` first.”

- If `/Module/{M}/php/` missing => **error**
  - Guidance: “Run `WebSDK_Module_Structure` first.”

NoDelete policy:
- Kisi bhi existing file/folder ko delete nahi karna.
- Create missing file allowed (index.php), but missing parent folders => error (because structure workflow owns it).

---

## [5] Discover Examples (Mandatory: 4 examples)
Is workflow ko code generation se pehle **4 existing module class examples** collect karne hain.

Rule:
- Examples source:
  - `/Aliens/WebSDK/PHP.{Version}/Module/*/php/index.php`
- Pick 4 best matches:
  - 2 singular modules (single-table surface)
  - 2 plural modules (modules that clearly have `{ModuleName}_*.php` parts and/or multiple `{ModuleName}_*` tables) if available
- Save examples list in RunRecord:
  - `Examples.json` + `Examples.md`

Purpose:
- “Aliens clean pattern” follow ho.
- Naming + methods + structure consistent rahe.

---

## [6] Code Generation Rules (Unique to this workflow)
### 6.1 File contract
Output file: `/Module/{M}/php/index.php`

File must:
- Follow **AliensStyle header/footer**
- Follow **AliensGrade quality gates**
- Keep existing important logic (if updating)
- Be consistent with existing module conventions

### 6.2 Create vs Update
- If file does not exist:
  - Create a full class skeleton + standard methods
- If file exists:
  - Update only what is required by `Description` + Planning output
  - Do not remove important features
  - Maintain backward compatibility

### 6.3 Minimum capability set (expected)
Class should cover:
- Core CRUD (as per existing ecosystem patterns)
- Validation layer (basic)
- Error handling consistent with ecosystem
- Optional: helper methods aligned with module domain

> Exact method names and patterns **examples se infer** honge (Step [5]).

### 6.3A Modular Class Layout (Large-file Prevention) (SSOT; Backward Compatible)
When planning indicates `PartitionPlan.Enabled=true` OR Description requires modularization, generator MUST produce a modular layout:

**Files (per module):**
- Main entrypoint (backward compatible):
  - `/Aliens/WebSDK/PHP.{Version}/Module/{ModuleName}/php/index.php`
- Facade traits:
  - `/Aliens/WebSDK/PHP.{Version}/Module/{ModuleName}/php/_Trait.php`
- Part classes:
  - `/Aliens/WebSDK/PHP.{Version}/Module/{ModuleName}/php/{ModuleName}_{Part}.php`

**Main class rules (index.php):**
- MUST `require_once` the part files and `_Trait.php` using `__DIR__` before `class {ModuleName}`.
- MUST `use` the facade traits (e.g., `{ModuleName}_{Part}Facade`).
- MUST own and initialize part instances in `__construct()`:
  - Accept optional injection args for each part (testing/override safe).
  - Default instantiate if null.
- MUST preserve existing public method names and make them callable from the main object.
- MUST keep `$PHP['{Module}']['function'][]` complete for all public methods (including facade wrappers).

**Facade traits rules (_Trait.php):**
- Each trait defines wrapper methods that delegate to the part instance.
- Trait property MUST exist and be initialized by main class constructor.
- Wrapper methods MUST follow AliensStyle/logging/single-return rule (for new/touched methods).

**Part classes rules ({ModuleName}_{Part}.php):**
- Must be `final class {ModuleName}_{Part}`.
- Naming rule (NON-NEGOTIABLE): generator MUST NOT append a literal `Part` suffix to `{ModuleName}_{Part}`.
  - Correct: `W3_Account` (file `W3_Account.php`, import `import('W3/W3_Account')`)
  - Forbidden: `W3_AccountPart` / `{ModuleName}_{Part}Part`
  - If a derived/extracted `Part` token ends with `Part` (example: `AccountPart`), it MUST be normalized by stripping trailing `Part` before generating file/class/trait names.
- Must contain only the partition methods (e.g., `User_Address*`).
- Must follow AliensStyle + `$echo`/`d()` logging + single-return rule for new/touched methods.

**Part Create (File-scope) rule:**
- When target is `Module/FileBase`, generator MUST:
  - Create `{FileBase}.php` with class `{FileBase}`.
  - Ensure `index.php` requires the part file and constructs the part instance.
  - Keep backward compatibility: do not remove/rename any existing public methods.

**Hard compatibility rule:**
- Never remove/rename existing public methods during generation/update. If method already exists in main class, keep it and only delegate when plan explicitly asks.

### 6.4 Table structure (optional but recommended)
If module has SQL structure file:
- `/Module/{M}/sql/index.sql`
Then:
- Parse it to understand tables/columns (read-only)
- Use it to generate accurate CRUD scaffolding

If SQL missing:
- Do not invent schema
- Create methods that are schema-agnostic or mark TODO in safe way

### 6.5 Mandatory layout derived from reference modules
Reference modules (`Account`, `Withdraw`) define the canonical order + constructs. Generator MUST follow this sequence for every class file:
1. `<?php` + copyright banner + `declare(strict_types=1);` + immediate `Access();` call.
2. Full AliensStyle header block (`[HEADER]` or `[CODEHEADER]`) containing Version/FileVersion/Location/Runtime/AliensStyle/Status/Author/Notes/Purpose/Responsibilities/etc.
  - Header must also contain `Dependencies` (Modules/Helpers/Globals).
3. Bootstrap logging:
  - `$echo = '[Module][Bootstrap] ...'; d($echo, __LINE__, 'process');`
  - All future logs follow `[Module][Method] Message` pattern with `d()` severity tags (`process|success|error`).
4. Required imports + metadata:
  - `import('User');` (and any other module dependencies listed in planning).
  - `$PHP['{Module}']['modified']`, `['by']`, and sequential `$PHP['{Module}']['function'][N] = 'MethodSignature';` entries summarizing public API.
5. Class definition:
  - Class name == module name (PascalCase), braces on new lines as in references.
  - Provide `__construct()` with deterministic `$echo` logs for init start/end.
6. Method blocks:
  - Each method preceded by `// ============================================================================` section banner.
  - Include inline comment scaffold capturing Developer/Date/Input/Engine/Output/Dependencies exactly like reference files.
    - `Dependencies` must list the helpers/engines used inside the method (e.g., `Row()`, `Rows()`, `RowNew()` etc.).
  - Public methods arranged in logical order: lifecycle helpers (bootstrap/sign-in), CRUD (`Module`, `Modules`, `ModuleNew`, `ModuleUpdate`, `ModuleStatus`), followed by domain helpers (e.g., `WithdrawManager`).
  - Preserve legacy signature shapes (`function Method($Q=null)` or `function Method($UID=null)`), no strict typing inside to maintain backward compatibility.
7. Helper usage conventions:
  - Use existing ecosystem helpers (`UID_Encode`, `Row()`, `Rows()`, etc.) rather than inventing new infrastructure.
  - Validation steps expressed via `$echo` logs before DB or helper calls.
  - No exceptions thrown; return arrays/strings exactly as legacy modules do.

8. Code footer (mandatory):
  - After the closing `}` of the class, append a structured AliensStyle footer block.
  - Footer must include:
    - `[CODE FOOTER]` (or `[CODEFOOTER]`) title
    - Usage Guide (how to call the module via WebOS/router)
    - Router Map recap (method → handler)
    - Change Log (file version notes)
  - End the file with the canonical divider note:
    - `// Divider: Append future footer notes above this line to keep layout consistent.`

Generator MUST refuse to output a class if this ordering or logging contract cannot be honored (raise actionable error pointing to missing planning data instead of emitting malformed file).

### 6.6 Logger Coverage + Finish Pair

- Every generated/updated method must start with `[Module][Method] Started` (`d($echo, __LINE__, 'progress')`) and end with `[Module][Method] Completed` (or `Failed`) using `success`/`error` severity. The workflow must fail if any branch exits without emitting both logs.
- When methods call AliensCRUD helpers (`Row*`, `Rows*`, `UID*`), insert before/after logs describing the payload and result so QA can replay behavior purely from logs.
- Loop-heavy routines (attendance sync, batch status) must suffix the log with iteration context (`: Item #{$Index}`) just like the Account module. This requirement is non-optional and should be validated during diff review.

### 6.7 Non-Static API + Row Naming
- Module classes must remain instance-based; do not generate `static` methods or pseudo-engine dispatchers.
- Primary APIs must follow the canonical CRUD family: `Module()`, `Modules()`, `ModuleNew()`, `ModuleUpdate()`, `ModuleStatus()`.
- If module uses 2+ tables (cluster), secondary table CRUD must use underscore naming and full CRUD family:
  - `Module_Table()`, `Module_Tables()`, `Module_TableNew()`, `Module_TableUpdate()`, `Module_TableStatus()`
- Method names mapped to AliensCRUD helpers must mirror the helper used inside (Row, Rows, RowNew, RowUpdate, RowStatus). Planning output should confirm the CRUD grid; if unknown, stop and request clarification instead of inventing new names.
- Do not create wrapper helpers (`RowHelper/RowsHelper/RowNewHelper/RowUpdateHelper/RowStatusHelper`) inside modules; call Row-family directly.
- WebSDK modules must not add `function_exists()` guards for core runtime primitives.
- Database table prefix must never be hardcoded in module defaults/properties/manifests. Always use base table names only because Row-family applies `prefix.prefix1` internally.
- `$PHP['{Module}']['function'][]` entries must list every public signature so downstream tooling stays accurate.

English-only + secret safety (Mandatory):
- Developer comments and internal `$echo` log strings must be English-only.
- Never log or hardcode secrets in logs (API keys, private keys, tokens, mnemonics). If a legacy module already contains secrets, the generator must not print them or duplicate them in new logs.

### 6.8 Function Micro-Headers + Spacing
- Prior to each method divider, insert a comment micro-header capturing Developer, LastTouched date, Inputs, Outputs, and Dependencies. Example:
  - `// Developer : AlienCyborg (2025-12-24)`
  - `// Inputs    : $Payload (Teacher data)`

### 6.9 Section divider contract (must match reference modules)
- Every public method must be preceded by the canonical 3-line divider using `//` comments:
  - `// ============================================================================`
  - `// [SECTION] {Module}::{Method}()`
  - `// ============================================================================`
- Avoid block-comment section dividers (`/* ... */`) for method banners in new/updated files.
  - `// Output    : array|null`
- Function code-header blocks (step-by-step plan/contract) must be inside the function body and must not be merged with outer section dividers.
- Enforce two blank lines between methods and one blank line before/after every divider block. The generator should refuse to emit squashed sections even if the legacy file lacked spacing; auto-formatting is part of the upgrade.
- If an existing file already contains micro-headers, update them to match the new template rather than deleting. Missing metadata is treated as a style failure.

---

## [7] Safety + Approval Enforcement
- Approval mode: manifest-driven
- If manual approval required:
  - Generate Approval request:
    - `/Aliens/.Alien/{Developer}/Approvals/{ApprovalId}.md`
  - Include:
    - Target module
    - Planned file changes summary
    - Risk assessment (low/medium/high)
  - Do not write prod file until approved

---

## [8] RunRecord (Mandatory)
For each target module run:
Create:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`

Write:
- `Input.json` (Targets item + Description)
- `ResolvedPaths.json`
- `Examples.md` + `Examples.json`
- `DiffSummary.md` (what changed)
- `OutputFiles.json`
- `Status.json` (success/failed/partial + errors)

---

## [9] Standard Errors (Must be actionable)
- `ERR_TARGET_INVALID`: Invalid module name
- `ERR_MODULE_MISSING`: Module folder not found (run structure workflow)
- `ERR_PHP_FOLDER_MISSING`: php/ folder missing
- `ERR_APPROVAL_REQUIRED`: manual approval pending
- `ERR_WRITE_FAILED`: cannot write file (permission/lock)

All errors must include:
- Exact target
- Exact path
- Suggested next workflow (if applicable)

---

## [10] Completion Criteria
A run is **success** only if:
- `index.php` created/updated successfully
- AliensStyle + AliensGrade applied (and logged)
- RunRecord complete

If approval pending => **pending** (not success).

---

## [11] Notes for Future
- If later you add new global rules affecting all singular-code workflows:
  - Add them in `Workflows/ACC/_Common/Workflow_Singular-Code.md`
  - Do not duplicate here
