---
RefId: ACC.Targets
Type: ACC.Reference
Scope: TargetsGrammar
Status: Stable
Version: 26.00.00
LastUpdated: 2025-12-24
Purpose: >
  Defines the universal grammar and normalization rules for the ACC Workflow() Targets argument.
  This is a shared reference used by ALL workflows (Plural + Singular).
Compatibility:
  ACC: ">=26.00.00"
  WebOS: ">=PHP.26.00.00"
NonNegotiables:
  - CommaMeansRepeatRun: true
  - NoDelete: true
  - ModifyOnlyForUpdateWorkflows: true
  - MissingTargetIsErrorForUpdate: true
---

# ACC.Targets.md

## 1) Core Meaning of `Targets`
ACC me `Workflow(WorkflowId, Targets, Description)` ka **Targets** argument ek string hota hai jo 1 ya zyada **Target Items** ko define karta hai.

Hard clarification:
- `Targets` ka role sirf **identifier/name** dena hai.
- `Targets` ko kabhi “mode/keyword” na samjho.
  - Example: `WebOS.Troubleshoot` ek normal engine name hai.

- Agar comma `,` use hua hai => **same workflow ko multiple times run** karna hai, har item ke liye ek run.
- Target ka actual type (Engine/Module/API/Component/Template/Brand/etc.) **WorkflowId decide karega**.

Examples:
- `Workflow("WebOS_Engine_PHP", "WebOS.Cache, WebOS.Auth", "...")`
- `Workflow("WebSDK_Module_API", "User/UserNew, Account/AccountUpdate", "...")`
- `Workflow("WebApp_Component", "Exchange/Balance, Swap/DepositNew", "...")`
- `Workflow("WebApp", "Exchange, Swap", "...")`

---

## 2) Target List Grammar (Top Level)
**Targets string** => **TargetList[]**.

### 2.1 Split rule (comma)
- Split by `,`
- Trim whitespace around each item
- Empty items ignore (but log warning)

Example:
- `"User/UserNew,  User/Users  , Web3/Web3_Accounts"`  
  becomes:
  - `User/UserNew`
  - `User/Users`
  - `Web3/Web3_Accounts`

### 2.2 Repeat-run meaning
Comma ka matlab “multiple targets” bhi ho sakta hai, aur “same workflow ko repeat run” bhi.
Implementation wise dono same hain: **har item par ek separate run**.

---

## 3) Single Target Item Grammar
Har TargetItem generally 2 patterns me aata hai:

### Pattern A: NameOnly
`NameOnly` = ek simple name without slash.

Used when workflow scope is “parent-level”:
- App-level: `Exchange`
- Module-level: `User`
- Brand-level: `Binance`
- Engine-level: `WebOS.Cache` (base-name only; extension decided by workflow)

Examples:
- `Exchange`
- `User`
- `Binance`
- `WebOS.Cache`

### Pattern B: Parent/Child
`Parent/Child` = parent container + child entity.

Used when workflow scope is “inside parent”:
- Module/API: `User/UserNew`
- App/Component: `Exchange/Balance`
- App/Template: `Exchange/Home`
- Module/Cron: `User/Users`
- Module/Webhook: `User/UserUpdate`

Examples:
- `User/UserNew`
- `Exchange/DepositNew`

**Rule:** slash ka meaning sirf “parent-child relation” hai.  
Child kya represent karta hai (API/Component/Template/etc.) woh WorkflowId decide karega.

---

## 4) Dotted Names (WebOS Engines)
Dotted name format:
- `WebOS.Cache`
- `WebOS.Auth`
- `WebOS.DB`

**Important rule:** `WebOS.Cache` ka matlab “file base-name” hai, extension nahi.  
Workflow decide karega target engine ka location:
- PHP Engine: `/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.Cache.php`
- JS Engine:  `/Aliens/WebOS/PHP.26.00.00/unit/js/WebOS.Cache.js`
- Bash:       `/Aliens/WebOS/PHP.26.00.00/unit/bash/WebOS.Cache.sh`
- SQL:        `/Aliens/WebOS/PHP.26.00.00/unit/sql/WebOS.Cache.sql`

Is mapping ka full resolver `ACC.Paths.md` me define hoga.  
Yeh file sirf grammar + normalization define karti hai.

### 4.1 WebOS EngineRouter Payload Passing (compatibility note)
WebOS EngineRouter/facade calls ke arguments numeric payload array me pass karta hai:
- `WebOS::EngineAction($a, $b)` → `$Payload = [$a, $b]`

Is liye engine implementations ko `$Payload` numeric args style ko support karna chahiye.

---

## 5) Underscore Rule (Cluster vs Standard)
### 5.1 Standard Parent (no underscore)
Example: `User`

Meaning:
- Standard module/theme/brand/app style.
- Typically single main table (module case) and single primary class.
- Naming is clean (no prefix-based grouping expected).

### 5.2 Cluster Parent (underscore present)
Example: `User_` (or parent contains `_`)

Meaning:
- Cluster module (multiple tables with prefix), e.g. `User_*` tables.
- Still may have single main class, but methods/tables can be many with prefix.
- Child items (APIs/components/templates) may also follow prefix conventions.

**Enforcement:**
- Parser does not block underscore; it only flags `ParentType=Cluster`.
- Workflow may use this hint to locate tables/classes faster.

---

## 6) Normalization Rules (Must Apply Everywhere)
For every TargetItem:
1) Trim leading/trailing spaces
2) Collapse multiple spaces inside names (optional but recommended)
3) Disallow control chars, newlines
4) Disallow path traversal sequences like `../` or `..\` (security)
5) Keep original case as-is (case rules are filesystem + ecosystem dependent)

### Allowed characters (recommended safe set)
- Letters, numbers
- `_` underscore
- `-` hyphen
- `.` dot (for engines, namespaces)
- `/` slash (only one level recommended: Parent/Child)

If a workflow wants stricter rules, it may validate further, but this ref defines the safe baseline.

---

## 7) Target Classification Output (for engines to use)
After parsing, each item should be represented as:

- `Raw`: original
- `Normalized`: trimmed, validated
- `HasSlash`: true/false
- `Parent`: string (if slash)
- `Child`: string (if slash)
- `HasDot`: true/false
- `IsClusterParent`: true/false (if parent has `_`)
- `RepeatIndex`: integer (order in list)

Example classification:
- `User/UserNew`
  - Parent=User, Child=UserNew, IsClusterParent=false
- `Web3/Web3_Accounts`
  - Parent=Web3, Child=Web3_Accounts, IsClusterParent=true (because parent contains `_`? no; only if parent has underscore)
- `WebOS.Cache`
  - HasDot=true, NameOnly

---

## 8) Existence Rules (Update vs Create)
This file defines universal policy hints; execution rule is enforced by `ACC.Safety.md` and per-workflow logic:

- If WorkflowId contains `_Update`:
  - **Target must exist**
  - Missing => error (do not create silently)
- If WorkflowId is “create/new” category:
  - Missing parent may be created depending on workflow (but never delete)
  - Missing child creation allowed (if workflow supports it)

---

## 9) Common Mistakes + How system should respond
### 9.1 Extra commas
`"User/UserNew,,Account/AccountUpdate,"`
- Empty items ignored, warning logged.

### 9.2 Wrong slash usage
`"/UserNew"` or `"UserNew/"`
- Invalid => error: missing parent or child.

### 9.3 Too deep paths
`"User/API/UserNew"`
- Default policy: reject (too deep) unless workflow explicitly supports deep hierarchy.

### 9.4 Unsafe patterns
`"../secrets"` or `"User/../../x"`
- Reject immediately (security).

---

## 10) Quick Examples (ready to copy)
### WebOS Engines
- `WebOS.Cache`
- `WebOS.Auth, WebOS.DB`

### WebSDK Module APIs
- `User/UserNew`
- `User/UserNew, User/Users, Web3/Web3_Accounts`

### WebApp Components
- `Exchange/Balance`
- `Exchange/DepositNew, Exchange/Withdraws`

### WebApp Whole App
- `Exchange, Swap`

---

## 11) Implementation Notes (for ACC Engines)
- Parsing should be deterministic and stable.
- Preserve ordering.
- Always return a structured TargetList with per-item metadata.
- Logging should include normalized form + classification.
- Never mutate filesystem based on raw string without normalization.

