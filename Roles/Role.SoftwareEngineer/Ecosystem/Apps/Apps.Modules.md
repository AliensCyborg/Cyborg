# Apps.Modules — Detailed Version

This document defines the **complete rules, patterns, and structure** for how **Modules** work inside any App in the Aliens WebOS ecosystem, specifically for the **AlienCyborg → Role.SoftwareEngineer → Ecosystem → Apps** layer.

---

## 1. What is a Module?

A **Module** is the smallest _business-focused_ building block inside an App.

- App = container of multiple Modules  
- Module = focused on one business concept  

Examples:
- `User` module  
- `Wallet` module  
- `KYC` module  
- `Transaction` module  
- `Staking` module  
- `Notification` module  

A good module:
- Has a single clear responsibility  
- Can be reused across apps (when designed as generic)  
- Can be easily tested and debugged  
- Can be maintained independently  

---

## 2. Standard Module Folder Structure

Every module follows this structure inside an App:

```text
App/{AppName}/Modules/{ModuleName}/
  ├── Logic.php
  ├── Validation.php
  ├── Service.php
  ├── Hooks.php
  ├── Config.json
  ├── Screens/
  │     ├── List.php
  │     ├── Add.php
  │     ├── Edit.php
  │     └── View.php
  ├── API/
  │     ├── Create.php
  │     ├── Update.php
  │     ├── List.php
  │     ├── View.php
  │     └── Delete.php (soft delete)
  └── Tests/
        ├── Unit/
        └── Integration/
```

If a module is shared as a **global unit**, it may also live under:

```text
Unit/Modules/{ModuleName}/
```

---

## 3. File Responsibilities

### 3.1 Logic.php
- Contains the **core business logic** of the module.
- It should **not** directly access `$_GET` or `$_POST`.
- It should **not** output HTML.
- It may call:
  - `Row()` / `WebOS_Row`
  - Other module services
  - Validator methods

Typical functions:
- `ModuleInit()`
- `ProcessBusinessRules()`
- `PostProcess()`

---

### 3.2 Validation.php
- Responsible for:
  - Input validation
  - Business rule checks
  - Sanitization
- Never hits DB directly (only through services if really needed).

Common patterns:
- `ValidateCreate($Data)`
- `ValidateUpdate($Data)`
- `ValidateFilter($Filter)`
- `ValidatePermissions($User, $Action)`

---

### 3.3 Service.php
- Wraps **DB operations** for the module using:
  - `Row()`
  - `WebOS_Row`
- Provides **high-level service functions** like:
  - `CreateEntity()`
  - `UpdateEntity()`
  - `GetById()`
  - `GetList()`
  - `SoftDelete()`

Service rules:
- **Never** expose raw SQL.
- **Always** respect:
  - Status model (`status` = 1/0/9)
  - Ecosystem/App/Tenant mapping
- **Always** log important actions (using `$echo` style if applicable).

---

### 3.4 Hooks.php
- Provides module-level hook points.
- Hooks are fired at key lifecycle stages:

Examples:
- `BeforeCreate($Data)`
- `AfterCreate($Entity)`
- `BeforeUpdate($Data, $OldEntity)`
- `AfterUpdate($Entity)`
- `BeforeDelete($Entity)`
- `AfterDelete($Entity)`

Hooks are used for:
- Audits  
- Notifications  
- Cascading updates  
- Cache invalidation  

---

### 3.5 Config.json
Each module has a small config file:

```json
{
  "name": "Transaction",
  "slug": "transaction",
  "version": "1.0.0",
  "status": 1,
  "permissions": {
    "admin": ["create", "update", "view", "list", "delete"],
    "user": ["create", "view", "list"],
    "guest": []
  }
}
```

This enables:
- App to auto-detect modules
- Permission engine to load rights
- UI engine to render menus/buttons

---

### 3.6 Screens/
Screens are the **UI layer** for this module.

Standard pages:
- `List.php` → data list table  
- `Add.php` → add form  
- `Edit.php` → edit form  
- `View.php` → read-only detail view  

Rules:
- Screens call **Service + Logic**  
- Screens should NOT contain heavy business logic  
- Screens use theme/layout engine  
- Screens use standardized tables & components  

---

### 3.7 API/
API files implement module-specific endpoints:

- `Create.php`  
- `Update.php`  
- `List.php`  
- `View.php`  
- `Delete.php` (soft delete)  

Rules:
- All APIs:
  - Validate auth
  - Validate permissions
  - Call Validator
  - Call Service
  - Return JSON only (no HTML)

---

### 3.8 Tests/
Optional but recommended.

- Unit tests for:
  - Validation
  - Service
  - Logic
- Integration tests for:
  - API endpoints
  - Module behaviour with DB

---

## 4. Module Lifecycle

### 4.1 Load Sequence
For a module request, typical flow:

```text
1. Request comes in (Screen or API)
2. App Engine resolves App and Module
3. Module.Config.json is read (if not loaded already)
4. Permissions checked
5. Validation rules loaded
6. Service & Logic used to process data
7. Hooks executed (Before/After)
8. Response (UI / JSON) returned
```

---

## 5. Module Types

### 5.1 Core Modules
- Critical system modules
- Example:
  - User
  - Ecosystem
  - App
  - Tenant
  - Role
- Changes must be done very carefully.

### 5.2 Business Modules
- Implement business logic for tenants
- Example:
  - Transactions
  - KYC
  - Wallet
  - ROI

### 5.3 Utility Modules
- Provide cross-cutting tools
- Example:
  - Notification
  - Audit
  - Logs
  - Settings

### 5.4 AI Modules (for AlienCyborg)
- Modules built for:
  - Code generation
  - Refactoring
  - Documentation
  - Automation of tasks

---

## 6. Naming Rules

- Folder: `Modules/{ModuleName}/`
- PascalCase for PHP class names:
  - `TransactionService`
  - `TransactionValidator`
- Methods in clear verbs:
  - `CreateTransaction()`
  - `UpdateTransaction()`
  - `GetTransactionList()`

Avoid:
- Random naming like `x1()`, `doit()`, `temp()`.

---

## 7. Security Rules for Modules

1. No raw SQL in modules  
2. All DB calls must go through **Service** → **Row Engine**  
3. All input must pass through **Validation.php**  
4. All sensitive actions must use **Hooks** for audit  
5. Modules must respect:
   - Ecosystem
   - App
   - Tenant
   - Role
   - Status model

---

## 8. How Modules Talk to Each Other

Modules can interact via:
- Services:
  - `UserService::GetById()`
  - `WalletService::GetBalance()`
- Events:
  - `Event::Fire('Transaction.Created', $Payload)`
- Shared Units:
  - `Unit/Notification/Service.php`

Direct file `include`/`require` should be avoided unless necessary.

---

## 9. AI Automation for Modules (Inside AlienCyborg)

The AlienCyborg agent (SoftwareEngineer role) can:

- Generate new module skeletons  
- Add Logic/Validation/Service based on requirements  
- Attach module into App manifest  
- Create Screens automatically  
- Create API endpoints  
- Add basic tests  
- Auto-generate config/permissions  

AI Safety Rules:
- Never touch Kernel/System folder when editing modules  
- Keep module boundaries clear  
- Use existing patterns (`$echo`, Row engine, status fields)  

---

## 10. Module Versioning & Migration

- Every module should define:
  - Version in `Config.json`
- Breaking changes:
  - Require migration script
  - Change major version
- Minor changes:
  - Update minor version field
- AlienCyborg should:
  - Generate migration checklist
  - Generate db migration stubs

---

## 11. Checklist for a New Module

Before using a module in production:

- [ ] Module folder exists  
- [ ] Logic.php created  
- [ ] Validation.php created  
- [ ] Service.php created  
- [ ] Hooks.php created  
- [ ] Screens/List,Add,Edit,View created (if UI module)  
- [ ] API endpoints created (if API module)  
- [ ] Config.json defined  
- [ ] Permissions mapped  
- [ ] Tests (at least smoke tests) written  
- [ ] Registered in App manifests  
- [ ] Security review done  

---

## 12. Conclusion

Modules are the **core building block** of Apps inside Aliens WebOS.  
Well-designed modules:

- Make apps easier to build  
- Make scaling easier  
- Make AI automation smoother  
- Make debugging simpler  
- Reduce duplication  

This document is the **primary reference** for module design for the AlienCyborg SoftwareEngineer role.
