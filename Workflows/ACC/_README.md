# ACC Workflows — README (v26)
# File: Workflows/ACC/_README.md
# Project: ACC (AlienCyborg Codes)
# Agent: AlienCyborg
# Ecosystem: Aliens WebOS / WebSDK / WebApps / WebBrand
# Status: Active
# Version: 26.00.00
# Last Updated: 2025-12-24

---

## 1) ACC kya hai? (short + clear)
ACC (AlienCyborg Codes) ek **workflow-first AI execution language** hai jo **AlienCyborg Agent** ko *clean, predictable, auditable* tareeke se kaam karwata hai.

ACC ka goal:
- Humans ke liye **extra easy** commands
- **Low-bug** execution (predefined steps + validation)
- Sirf “software” nahi, **complete business/system** build karwana
- Aliens Ecosystem (WebOS/WebSDK/WebApps/WebBrand) ke rules ke according kaam karwana

ACC “traditional programming language” nahi hai.  
ACC ek **command + orchestration language** hai.

---

## 2) Workflow kya hota hai?
ACC me **Workflow** smallest executable unit hai.

Workflow:
- Human intent ko structured steps me convert karta hai
- Predefined rules follow karta hai
- Safe-by-default hota hai
- Output + audit generate karta hai

Execution syntax:
```text
Workflow(WorkflowID, Targets, Description)
```

**Arguments:**
- **WorkflowID**: workflow ka unique id (system me already defined file se match)
- **Targets**: 1 ya multiple items (comma-separated)
- **Description**: human language me custom requirement (Hindi/English/Hinglish)

Important clarifications (no confusion rules):
- `Targets` hamesha **identifier/name** hota hai (resource name), koi “mode/keyword” nahi.
  - Example: `WebOS.Troubleshoot` ek normal engine name hai, special mode nahi.
- `Description` mandatory hai. Agar empty/missing ho to workflow **fail** karega (ref: `ACC_ERR_DESCRIPTION_REQUIRED`).

Example:
```text
Workflow("WebSDK_Module_API", "User/UserNew, Account/AccountUpdate", "Aese kr do wese kr do")
Workflow("WebApp_Component", "Exchange/DepositNew, Exchange/Withdraws", "Dono components ka UI premium hona zaroori hai")
Workflow("WebOS_Engine_PHP", "WebOS.Cache, WebOS.Auth", "Engines enterprise-grade generate karo")
```

---

## 3) Targets Grammar (important)
Targets ka actual meaning **WorkflowID** decide karta hai.

Common rules:
- **Comma ( , )** = same workflow ko multiple times run karo (ordered list)
- **Slash ( Parent/Child )** = relationship present karta hai
  - `User/UserNew` = `User` container, `UserNew` item inside
  - `Exchange/Balance` = `Exchange` container, `Balance` item inside
- **Dot notation** like `WebOS.Cache` = base-name without extension
  - Extension workflow decide karega (`.php`, `.js`, `.sql`, `.sh`)

### 3.1 WebOS EngineRouter payload note (very important)
WebOS engines me facade/EngineRouter call arguments ko numeric payload array me pass karta hai:
- `WebOS::SecurityValidateEmail($arg1)` → `$Payload = [$arg1]`
- `WebOS::SecuritySignURL($arg1, $arg2)` → `$Payload = [$arg1, $arg2]`

Is liye engine implementations ko numeric `$Payload` ko correctly parse/extract karna mandatory hai.

### 3.2 Return-type compatibility (enterprise rule)
Repo me same action different contexts me use ho sakta hai:
- `bool` (validation/allow checks)
- `string` (token/url/hash/random)
- `array envelope` (expects keys like `success`, `data`)

Foundational engines (Security/Auth/API/Cache) me backward compatibility preserve karna mandatory hai; docs me return-type notes/matrix include hon.
- **Underscore rule**
  - `User` (no underscore) = Standard module (usually 1 main table)
  - `User_` (underscore present) = Cluster module (multiple tables with prefix)

Detailed grammar refs:
- `Workflows/ACC/_Refs/ACC.Targets.md`

---

## 4) Folder Structure (new architecture)
```text
Workflows/ACC/
├─ _README.md
│
├─ _Common/
│  ├─ Workflow_Plural.md
│  ├─ Workflow_Singular.md
│  ├─ Workflow_Singular-Planning.md
│  ├─ Workflow_Singular-Code.md
│  └─ Workflow_Singular-Documentation.md
│
├─ _Refs/
│  ├─ ACC.Targets.md
│  ├─ ACC.Paths.md
│  ├─ ACC.Memory.md
│  ├─ ACC.Output.md
│  ├─ ACC.Safety.md
│  ├─ ACC.Manifest.md
│  ├─ ACC.Errors.md
│  ├─ ACC.UI.Premium.md
│  ├─ WebOS_Engines.md
│  ├─ WebOS_System.md
│  └─ WebSDK.md
│
├─ Memory/
├─ WebOS/
├─ WebSDK/
├─ WebApp/
└─ WebBrand/
```

### Why `_Common/`?
`_Common/` me **common rule-engine files** hoti hain (workflows nahi).  
Inka kaam: 10,000+ workflows me shared rules ko **central** rakhna taaki ek jagah update se sab update ho jaye.

### Why `_Refs/`?
`_Refs/` me **knowledge + rules** (targets, paths, safety, output schema, engines index, system functions index) rakhte hain.  
Workflows in refs ko read karke consistent decisions lete hain.

---

## 5) Workflow Types (core concept)
ACC me workflows ko 2 levels par socho:

### 5.1) Singular Workflow
- Singular workflow ke andar **kisi workflow ko call karna allowed nahi** (default rule)
- Ye focused task karta hai (Planning OR Code OR Documentation etc.)

Common rules:
- `Workflows/ACC/_Common/Workflow_Singular.md`

### 5.2) Plural Workflow
- Plural workflow **orchestrator** hota hai
- Ye targets list par loop karke **multiple child workflows** call karta hai

Common rules:
- `Workflows/ACC/_Common/Workflow_Plural.md`

---

## 6) “Workflow Family” pattern (recommended standard)
Bohot saare cases me 1 “Main Workflow” ko complete karne ke liye 3 child workflows run hote hain:

1) `-Planning`  (Singular-Planning)
2) `-Code`      (Singular-Code)
3) `-Documentation` (Singular-Documentation)

Example family:
- `WebSDK_Module_API.md`              (Plural orchestrator)
- `WebSDK_Module_API-Planning.md`     (Singular-Planning)
- `WebSDK_Module_API-Code.md`         (Singular-Code)
- `WebSDK_Module_API-Documentation.md`(Singular-Documentation)

Note:
- **Har WorkflowID ki exactly 1 file hoti hai**.
- Family me har child ka apna unique WorkflowID hota hai.
  - Example: `WebSDK_Module_API-Planning` is also a WorkflowID.

---

## 7) Safety + Permissions (non‑negotiables)
Global safety principles:
- **Files delete kabhi nahi** (NoDelete = true)
- Create + edit allowed
- Production code modify allowed (policy-controlled)
- Database touch allowed (policy-controlled)
- `_Update` workflows: **modify-only**, missing target => **error**

Source of truth:
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

## 8) Modes (Agent behavior)
Developer manifest me mode decide hota hai:

- **Employee Mode**
  - Agent continuously tasks poll karta rahega (Task folder / queues)
  - Tasks khatam hone ke baad bhi watch mode me rahega

- **Assistant Mode**
  - Agent bina instructions ke kaam nahi karega
  - Explicit Workflow calls required

Manifest path (per developer):
- `/Aliens/manifest.json`

Manifest schema ref:
- `Workflows/ACC/_Refs/ACC.Manifest.md`

---

## 9) Approvals (Auto vs Manual)
ApprovalMode manifest se decide hota hai:

- **auto**
  - safe + predefined actions direct run
  - still audit logs mandatory

- **manual**
  - approvals generate honge
  - risky actions (prod/db/security) approval ke bina proceed nahi honge

Note (enterprise discipline):
- Manual approval mode me approval record + run-record artifacts ko post-run verify karna mandatory hai (audit-grade).

Recommended approvals location:
- `/Aliens/.Alien/{Developer}/Approvals/`

---

## 10) Output + Audit (mandatory)
Har run ka record banta hai:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/`
- per-target items:
  - `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/`

Planning outputs:
- `/Aliens/.Alien/{Developer}/Planning/`

Documentation outputs (final docs):
- `/Aliens/Docs/{Domain}/...`

Output schema ref:
- `Workflows/ACC/_Refs/ACC.Output.md`

---

## 11) Domains (Workflows responsibilities)
### WebOS
Engines/System level work:
- PHP engine: `/Aliens/WebOS/PHP.26.00.00/Engine/WebOS.X.php`
- JS engine: `/Aliens/WebOS/PHP.26.00.00/Unit/js/WebOS.X.js`
- SQL engine: `/Aliens/WebOS/PHP.26.00.00/Unit/sql/WebOS.X.sql`
- Bash engine: `/Aliens/WebOS/PHP.26.00.00/Unit/bash/WebOS.X.sh`

### WebSDK
Modules/APIs/Crons/Webhooks:
- `/Aliens/WebSDK/PHP.26.00.00/Module/{Module}/...`

### WebApp
Apps + Themes + Components + Templates:
- `/Aliens/WebApp/{App}/26.00.00/theme/{App}_Basic/User/...`

### WebBrand
Brand themes (override layer for many apps):
- `/Aliens/WebBrand/{Brand}/PHP.26.00.00/Theme/{Brand}_Basic/...`

Paths resolver ref:
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

## 12) Change Policy (strict)
- `_Common/` me changes **extreme care** ke saath (10,000+ workflows impact)
- `_Refs/` me changes versioned + documented
- Individual workflows me unique rules allowed, but common files me sirf shared rules

---

## 13) Final Note
ACC workflows folder **AlienCyborg ka brain** hai:
- clean workflows => predictable AI
- strict rules => scalable ecosystem
- strong audit => enterprise trust

Agar aap koi naya workflow add kar rahe ho:
1) Decide: Plural vs Singular
2) Common files require karo
3) Refs follow karo
4) Outputs + logs mandatory

---
