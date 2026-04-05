---
RefId: ACC.Workflows.Index
Type: RegistryIndex
Scope: Workflows/ACC
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-20
Purpose: >
  Yeh file ACC workflow library ka authoritative index hai.
  Iska goal: ek hi jagah par saare workflows, unke types, file paths,
  child workflow chain, aur expected outputs ko crystal clear rakhna.
NonNegotiables:
  - NoDelete: true
  - PathPrefix: "Workflows/ACC/"
  - RefsFolder: "Workflows/ACC/_Refs/"
  - CommonFolder: "Workflows/ACC/_Common/"
---

# ACC.Workflows.Index.md

## [01] Overview
ACC me `Workflow(WorkflowId, Targets, Description)` ek **call** hai, aur `WorkflowId` ka matlab hai:
- ek **workflow file** exist karti hai library me
- us workflow ka **type** (Plural/Singular) fixed hota hai
- aur output + safety rules deterministic hote hain

Yeh index file ka purpose:
- Team ko instantly pata chale: **konsa workflow kis kaam ke liye hai**
- Aur AlienCyborg ko deterministic routing mil jaye: **WorkflowId -> exact file path + child chain**

---

## [02] Folder Map (Library Layout)
### A) Common Engine Files
- `Workflows/ACC/_Common/Workflow_Plural.md`
- `Workflows/ACC/_Common/Workflow_Singular.md`
- `Workflows/ACC/_Common/Workflow_Singular-Planning.md`
- `Workflows/ACC/_Common/Workflow_Singular-Code.md`
- `Workflows/ACC/_Common/Workflow_Singular-Documentation.md`

### B) Refs (Rules + References)
- `Workflows/ACC/_Refs/ACC.Targets.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Refs/ACC.Output.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`
- `Workflows/ACC/_Refs/ACC.Manifest.md`
- `Workflows/ACC/_Refs/ACC.Errors.md`
- `Workflows/ACC/_Refs/ACC.UI.Premium.md`
- `Workflows/ACC/_Refs/WebOS_Engines.md`
- `Workflows/ACC/_Refs/WebOS_System.md`
- `Workflows/ACC/_Refs/WebSDK.md`
- `Workflows/ACC/_Refs/ACC.Workflows.Index.md`  (this file)

### C) Domain Folders (Individual Workflows)
- `Workflows/ACC/WebSDK/` (v26 ACC-compliant workflows)
- `Workflows/ACC/WebApp/` (v26 ACC-compliant WebApps workflows)

Reserved (future ecosystems; namespace only):
- `Workflows/ACC/PyOS/`, `Workflows/ACC/PyApp/`, `Workflows/ACC/PySDK/`, `Workflows/ACC/PyBrand/`
- `Workflows/ACC/DartOS/`, `Workflows/ACC/DartApp/`, `Workflows/ACC/DartSDK/`, `Workflows/ACC/DartBrand/`
- `Workflows/ACC/SharpOS/`, `Workflows/ACC/SharpApp/`, `Workflows/ACC/SharpSDK/`, `Workflows/ACC/SharpBrand/`

Legacy / non-ACC-v26 folders (single-file workflows; not registered as strict v26 workflows):
- `Workflows/ACC/WebOS.OLD/`
- `Workflows/ACC/WebApp.OLD/`
- `Workflows/ACC/WebBrand.OLD/`

---

## [03] Naming Rules (Strict)
Har “main workflow” ke 4 files:

1) **Plural Orchestrator**
- `{WorkflowId}.md` (Type: Workflow_Plural)

2) **Child - Planning**
- `{WorkflowId}-Planning.md` (Type: Workflow_Singular-Planning)

3) **Child - Code**
- `{WorkflowId}-Code.md` (Type: Workflow_Singular-Code)

4) **Child - Documentation**
- `{WorkflowId}-Documentation.md` (Type: Workflow_Singular-Documentation)

Plural file ALWAYS calls (default order):
- `{WorkflowId}-Planning` -> `{WorkflowId}-Code` -> `{WorkflowId}-Documentation`

---

## [04] Workflow Registry (Authoritative)
> Notes:
> - `Targets` ka syntax `ACC.Targets.md` follow karega.
> - `_Update` workflows: **modify-only** + missing target => error.
> - Output rules: `ACC.Output.md`.
> - Approvals: `ACC.Safety.md` + developer manifest.

### 4.1 Common (Cross-Ecosystem) Workflows
| WorkflowId | Domain | Type | Plural File | Child Workflows | Target Examples |
|---|---|---|---|---|---|
| Meta | Common | Plural | Workflows/ACC/_Common/Meta.md | Meta-Planning, Meta-Code, Meta-Documentation | `(empty)` |
| Docs | Common | Plural | Workflows/ACC/_Common/Docs.md | Docs-Planning, Docs-Code, Docs-Documentation | `B-Commerce/README` |
| Workflows | Common | Plural | Workflows/ACC/_Common/Workflows.md | `(dynamic by planner row)` | `Tasks.csv` |

### 4.2 WebSDK Workflows
| WorkflowId | Domain | Type | Plural File | Child Workflows | Target Examples |
|---|---|---|---|---|---|
| WebSDK_Module_Class | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_Class.md | WebSDK_Module_Class-Planning, WebSDK_Module_Class-Code, WebSDK_Module_Class-Documentation | `User, Account` |
| WebSDK_Module_Class_Update | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_Class_Update.md | WebSDK_Module_Class_Update-Planning, WebSDK_Module_Class_Update-Code, WebSDK_Module_Class_Update-Documentation | `User, Account` |
| WebSDK_Module_API | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_API.md | WebSDK_Module_API-Planning, WebSDK_Module_API-Code, WebSDK_Module_API-Documentation | `User/UserNew, Account/AccountUpdate` |
| WebSDK_Module_API_Update | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_API_Update.md | WebSDK_Module_API_Update-Planning, WebSDK_Module_API_Update-Code, WebSDK_Module_API_Update-Doc | `User/UserNew, Account/AccountUpdate` |
| WebSDK_Module_SQL | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_SQL.md | WebSDK_Module_SQL-Planning, WebSDK_Module_SQL-Code, WebSDK_Module_SQL-Documentation | `Product` |
| WebSDK_Module_Cron | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_Cron.md | WebSDK_Module_Cron-Planning, WebSDK_Module_Cron-Code, WebSDK_Module_Cron-Documentation | `Product, Product/Product_OrdersPayment` |
| WebSDK_Module_Webhook | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_Webhook.md | WebSDK_Module_Webhook-Planning, WebSDK_Module_Webhook-Code, WebSDK_Module_Webhook-Documentation | `Product, Product/ProductStatus` |
| WebSDK_Module_Component | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_Component.md | WebSDK_Module_Component-Planning, WebSDK_Module_Component-Code, WebSDK_Module_Component-Documentation | `Product, Product/Product_MoneyCard` |
| WebSDK_Module_Screen | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_Screen.md | WebSDK_Module_Screen-Planning, WebSDK_Module_Screen-Code, WebSDK_Module_Screen-Documentation | `Product, Product/Product_Orders` |
| WebSDK_Module_JS | WebSDK | Plural | Workflows/ACC/WebSDK/WebSDK_Module_JS.md | WebSDK_Module_JS-Planning, WebSDK_Module_JS-Code, WebSDK_Module_JS-Documentation | `Teacher, Product` |

### 4.3 WebApps Workflows
| WorkflowId | Domain | Type | Plural File | Child Workflows | Target Examples |
|---|---|---|---|---|---|
| WebApp_Component | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Component.md | WebApp_Component-Planning, WebApp_Component-Code, WebApp_Component-Documentation | `AlienCyborg/AlienCyborg_DepositNew, Social/Social_Post` |
| WebApp_Component_Update | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Component_Update.md | WebApp_Component_Update-Planning, WebApp_Component_Update-Code, WebApp_Component_Update-Documentation | `AlienCyborg/DepositNew, Social/Post` |
| WebApp_CSS | WebApps | Plural | Workflows/ACC/WebApp/WebApp_CSS.md | WebApp_CSS-Planning, WebApp_CSS-Code, WebApp_CSS-Documentation | `E-Commerce, Trip` |
| WebApp_CSS_Update | WebApps | Plural | Workflows/ACC/WebApp/WebApp_CSS_Update.md | WebApp_CSS_Update-Planning, WebApp_CSS_Update-Code, WebApp_CSS_Update-Documentation | `Trip` |
| WebApp_Section | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Section.md | WebApp_Section-Planning, WebApp_Section-Code, WebApp_Section-Documentation | `AlienCyborg/Deposit_Header, AlienCyborg/Home_Hero` |
| WebApp_Section_Update | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Section_Update.md | WebApp_Section_Update-Planning, WebApp_Section_Update-Code, WebApp_Section_Update-Documentation | `AlienCyborg/Deposit_Header, AlienCyborg/Home_Hero` |
| WebApp_Screen | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Screen.md | WebApp_Screen-Planning, WebApp_Screen-Code, WebApp_Screen-Documentation | `AlienCyborg/Deposit, AlienCyborg/Home` |
| WebApp_Screen_Update | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Screen_Update.md | WebApp_Screen_Update-Planning, WebApp_Screen_Update-Code, WebApp_Screen_Update-Documentation | `AlienCyborg/Deposit, AlienCyborg/Home` |
| WebApp_Structure | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Structure.md | WebApp_Structure-Planning, WebApp_Structure-Code, WebApp_Structure-Documentation | `B-Commerce` |
| WebApp_Structure_Update | WebApps | Plural | Workflows/ACC/WebApp/WebApp_Structure_Update.md | WebApp_Structure_Update-Planning, WebApp_Structure_Update-Code, WebApp_Structure_Update-Documentation | `B-Commerce` |

### 4.4 Legacy Workflows (Not registered as strict ACC-v26)
These exist in repo but are **single-file legacy specs** (no `-Planning/-Code/-Documentation` split).
They are documented here for discovery, but not part of the strict registry until they are migrated.

#### WebOS.OLD
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_PHP.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_PHP_Update.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_JS.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_JS_Update.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_SQL.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_SQL_Update.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_Bash.md`
- `Workflows/ACC/WebOS.OLD/WebOS_Engine_Bash_Update.md`

#### WebApp.OLD
- `Workflows/ACC/WebApp.OLD/WebApp.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Update.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Structure.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Structure_Update.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Component.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Component_Update.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Template.md`
- `Workflows/ACC/WebApp.OLD/WebApp_Template_Update.md`

#### WebBrand.OLD
- `Workflows/ACC/WebBrand.OLD/WebBrand.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Update.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Structure.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Structure_Update.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Component.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Component_Update.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Template.md`
- `Workflows/ACC/WebBrand.OLD/WebBrand_Template_Update.md`

---

## [05] Output Locations (Quick Reference)
Detailed schema: `Workflows/ACC/_Refs/ACC.Output.md`

Common rules:
- Run logs: `/Aliens/.Alien/{{Developer}}/Runs/{{RunId}}/`
- Per target item: `/Aliens/.Alien/{{Developer}}/Runs/{{RunId}}/Items/{{RunItemId}}/`
- Planning: `/Aliens/.Alien/{{Developer}}/Planning/`
- Docs: `/Aliens/Docs/{{Domain}}/...`

---

## [06] Add New Workflow (How to extend)
When you add a new workflow, always do:
1) Create 4 files (Plural + 3 child) in correct domain folder
2) Update this registry table with:
   - WorkflowId
   - Plural file path
   - Child workflow chain
   - Target examples
3) (Recommended) also update `ACC.Workflows.Index.json` for machine routing

---

## [07] Changelog
- 26.00.00 (2025-12-24): Initial workflow registry index (WebOS/WebSDK/WebApps/WebBrand)
