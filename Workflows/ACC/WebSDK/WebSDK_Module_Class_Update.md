---
WorkflowId: WebSDK_Module_Class_Update
WorkflowName: WebSDK Module Class Update
Type: Workflow_Plural
Domain: WebSDK
Category: Module
Intent: "Update existing WebSDK module PHP classes (php/*.php) for one or multiple modules (NO auto-create)"
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-03
Inputs:
  - Name: Targets
    Type: string
    Required: true
    Example: "User, Account, Web3, W3/W3_Account"
  - Name: Description
    Type: string
    Required: true
    Example: "Existing class ko optimize karo, security hardening, methods add, docs update"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Plural.md"
Calls:
  - "WebSDK_Module_Class_Update-Audit"
  - "WebSDK_Module_Class_Update-Planning"
  - "WebSDK_Module_Class_Update-Code"
  - "WebSDK_Module_Class_Update-Documentation"
DefaultOrder: ["Audit", "Planning", "Code", "Documentation"]
PermissionsRequested:
  NoDelete: true
  ProdWrite: true
  DbTouch: false
  Network: false
Approval:
  Default: "inherit developer manifest (auto/manual)"
  Notes:
    - "Update-only workflow. Missing targets => error."
Outputs:
  Planning:
    Location: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.md"
  Code:
    PrimaryFile: "/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/index.php"
    SaveStrategy: "modify-only"
  Documentation:
    Location: "/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.md"
    SaveStrategy: "create-or-update"
Safety:
  NoDelete: true
  ModifyOnly: true
  MissingTargetIsError: true
---

# WebSDK_Module_Class_Update.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [00] Purpose
Yeh workflow **sirf UPDATE** ke liye hai: WebSDK ke module ki primary class file (`php/index.php`) ko improve/modify karna.
- Agar module ya class file exist nahi karti: **ERROR** (koi auto-create nahi).
- Multiple targets comma-separated hon to workflow loop me run hoga (same workflow multiple times, per target).

Example:
`Workflow("WebSDK_Module_Class_Update", "User, Account, Web3", "Class ko refactor + secure + performance improve")`

## [01] Mandatory Includes (Non-negotiable)
Is workflow me yeh common engines MUST apply hon:
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

## [02] Input Contract
- Targets: required string (comma separated list)
- Description: mandatory (human language). Blank/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`.

## [03] Target Resolution Rules (WebSDK Module)
Target items examples:
- `W3` -> ModuleName (update whole module php folder)
- `W3/W3_Account` -> ModuleName + FileBase (update only one file)
Rules:
- Whitespace trim, empty items skip (but if all empty -> error)
- Duplicate targets allowed but default behavior: **de-duplicate** unless Description explicitly says "same module par multiple passes"
- Target item is either:
  - `ModuleName`
  - `ModuleName/FileBase`
    - `FileBase` MUST be a php filename base WITHOUT extension (example: `W3_Account`, not `W3_Account.php`).
    - `FileBase` MUST NOT contain additional slashes, dots, or traversal sequences.
- ModuleName should match folder name under: `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/`

Singular Entity Name Rule (SSOT):
- ModuleName MUST be singular (never plural).
- ClassName and table names referenced for this module MUST be singular.
- If violated => fail fast with `ACC_ERR_NAME_MUST_BE_SINGULAR`.

Module Types (SSOT):
- **Singular Module**: exactly 1 required DB table `{ModuleName}`.
- **Plural Module**: multiple required DB tables + units prefixed by `{ModuleName}_` (e.g., `User_Address`, `User_Meta`).

Important:
- Type is decided by required tables/units, not by underscore presence in `ModuleName`.

Target meaning (NEW SSOT):
- `Workflow('WebSDK_Module_Class_Update', 'W3', '...')` means update **ALL** PHP class files directly under:
  - `/Aliens/WebSDK/{LatestVersion}/Module/W3/php/*.php`
  - This includes: `index.php`, `_Trait.php`, and any `W3_*.php` part classes and any other `.php` class files in that folder.
- `Workflow('WebSDK_Module_Class_Update', 'W3/W3_Account', '...')` means update **ONLY**:
  - `/Aliens/WebSDK/{LatestVersion}/Module/W3/php/W3_Account.php`

## [04] Execution Plan (Plural Orchestration)
Per Target, sequence:
0) `WebSDK_Module_Class_Update-Audit` (read-only audit)
1) `WebSDK_Module_Class_Update-Planning` (plan create)
2) `WebSDK_Module_Class_Update-Code` (apply update to code)
3) `WebSDK_Module_Class_Update-Documentation` (update docs)

If manifest says manual approval:
- Planning always allowed
- Code + Docs only after approval gate (Workflow_Plural rules decide gate behavior)

## [05] Child Workflow Contract
Call signature (MUST be consistent):
- `Targets` (single target item per call: `ModuleName` OR `ModuleName/FileBase`)
- `Description` (same description forwarded)
Each child workflow must:
- enforce ModifyOnly policy
- write RunRecord and Output summary

## [06] Safety Rules (Unique to Update Workflow)
- NO auto-create of:
  - module folder
  - class file `php/index.php`
  - supporting sql files
- NO destructive actions:
  - delete, rename, move, truncate
- Existing code must be preserved:
  - methods, comments, headers, AliensStyle rules
  - if any removal required, treat as ERROR and request explicit user instruction (via Description)

Mandatory rule set (must be applied during update):
- AliensStyle: SECTION divider + internal CODEHEADER blocks for touched methods.
- Single-return rule: exactly one return at end for each touched method; return is mandatory.
- Function code-header must include Purpose/Input/Output/Dependencies.
- Logging: `$echo` + `d()` entry/branch logs in touched methods.
- DB prefix law: remove/replace hardcoded prefixed table names encountered in touched code (use `prefix` or base-table patterns).
- English-only: developer comments/log strings must be English-only in touched code.
- No `function_exists()` guards inside WebSDK modules for core primitives.

Reference standard (non-negotiable for structure/order):
- `/Aliens/WebSDK/PHP.26.00.00/Module/Account/php/index.php`
- `/Aliens/WebSDK/PHP.26.00.00/Module/Withdraw/php/index.php`

Child workflows MUST enforce that updates do not break canonical module skeleton ordering (declare/Access/header/bootstrap/imports/$PHP manifest/class).

## [07] Outputs
- Planning saved to:
  `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.md`
- Code updated in place:
  `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/index.php`
- Docs updated/created:
  `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.md`

Additional Output Rule (NEW; file-scoped target):
If target is `ModuleName/FileBase` then outputs MUST be file-scoped to avoid overwriting module-wide artifacts:
- Planning:
  - `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.md`
- Docs (optional, if docs step runs):
  - `/Aliens/Docs/WebSDK/Module/{ModuleName}/Class.{FileBase}.md`

## [08] Errors (Minimum Set)
- `ERR_TARGET_EMPTY`
- `ERR_MODULE_NOT_FOUND`
- `ERR_CLASS_FILE_NOT_FOUND`
- `ERR_MANIFEST_APPROVAL_REQUIRED`
- `ERR_UNSAFE_OPERATION_BLOCKED`

## [09] Acceptance Criteria
Success tabhi jab:
- Planning file created for every target
- Code file updated without breaking syntax / AliensStyle header/footer rules
- Docs updated with latest methods list + usage
- RunRecord summary exists (as per Workflow_Universal)

## [10] Examples
1) Standard module update:
`Workflow("WebSDK_Module_Class_Update", "User", "Existing class ko optimize karo, auth checks add karo")`

2) Plural module update (multi-table + units):
`Workflow("WebSDK_Module_Class_Update", "User", "Plural module hai (User + User_Address + User_Meta). Tables mapping + modularization update karo")`

3) Multi-module update:
`Workflow("WebSDK_Module_Class_Update", "User, Account, Web3", "Security hardening + docs refresh")`

---

## [11] Execution Protocol Upgrade (Required)
Jab bhi aap `WebSDK_Module_Class_Update` run karte ho, iska matlab yeh hai:

### 11.1 Planning-first (Non-negotiable)
- Agent MUST first read the full target file and generate a checklist-style plan (see `WebSDK_Module_Class_Update-Planning`).
- Plan MUST be deterministic and file-specific (no generic advice-only output).

### 11.2 Default Mode = Step-by-step (User-gated)
Default behavior (unless Description says apply-all):
1) Apply **one** checklist rule across the whole file (modify-only).
2) Give a **short** status message: what rule done + lint status.
3) Ask a single confirmation line: `Next step apply kar doon? (ok)`
4) Only then proceed to next rule.

### 11.3 Override Mode = Apply All (Continuous)
If Description contains any of these triggers (case-insensitive):
- `apply all`
- `all rules`
- `continuous mode on`
- `saary rules`
- `bina puchy`

Then:
- Run the **entire checklist** end-to-end without asking between steps.
- Still emit a short per-step status line (but no wait).

### 11.4 Output Discipline (Short + Actionable)
After each step:
- 1–2 lines only
- Include: step name + quick result + next step name

Example:
`Done: English-only logs/comments (Web3 index.php). php -l OK. Next: SECTION dividers. Ok?`
