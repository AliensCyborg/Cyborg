# Apps.API.Flow — Detailed Version

This document defines the **standard API flow** for all Apps inside the Aliens WebOS ecosystem, specifically for the  
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Apps` layer.

It is written so that:
- Human developers
- AlienCyborg (SoftwareEngineer role)
- Other AI tools

all follow **one strict API pattern** for every App / Module.

---

## 1. Purpose of This Standard

- Make every API **predictable**, **secure**, and **easy to automate**.  
- Remove confusion about “API ka code kahan likhna hai, kaise likhna hai, kaise flow chalega”.  
- Allow AI to:
  - Auto-generate APIs
  - Auto-debug APIs  
  - Auto-optimize APIs  
  …without breaking WebOS rules.

---

## 2. Location of APIs in an App

Every App will keep its APIs in a clear structure:

```text
App/{AppName}/API/
  ├── index.php
  ├── {ModuleName}.Create.php
  ├── {ModuleName}.Update.php
  ├── {ModuleName}.Delete.php
  ├── {ModuleName}.View.php
  ├── {ModuleName}.List.php
  └── {CustomAction}.php
```

If API is tightly coupled with a specific module, it can also live inside:

```text
App/{AppName}/Modules/{ModuleName}/API/
```

But **recommended default** for normal REST-style endpoints is:

```text
App/{AppName}/API/
```

---

## 3. Standard API Request Lifecycle

When any API is called, the following flow MUST be followed:

```text
1. Request comes to WebServer (Nginx / Apache)
2. Route directs to WebOS Boot (Boot/os.php)
3. System + Kernel load (System/index.php, Kernel)
4. WebOS resolves:
   - Ecosystem
   - App
   - Tenant
   - User (i)
5. API file is located in App/{App}/API/
6. API file runs with strict pattern:
   a. Bootstrap local context
   b. Validate auth + permissions
   c. Load input data
   d. Validate input
   e. Call Service layer (Module Service)
   f. Run Hooks (Before/After)
   g. Build structured response
   h. Return JSON
```

---

## 4. API File Structure (Template Standard)

Every API file should follow this **fixed pattern**:

```php
<?php

// [1] Security & Bootstrap
RequireOnce 'Boot/os.php';   // or appropriate app bootstrap include
// Load System, Kernel, App context is already done by WebOS before this

// [2] Identify API & Context
$Echo = 'API : {AppName}.{ModuleName}.{Action} : Started.'; d($Echo);

// [3] Auth & Permission Check
$User = i; // current logged in user (array/constant)
$Echo = 'API : Checking permissions.'; d($Echo);
// Call Permission Engine / Module Permission function
$IsAllowed = CheckPermission('{ModuleName}', '{Action}', $User);
if( !$IsAllowed ){
  return JsonError('Permission Denied.', 403);
}

// [4] Load Input
$Echo = 'API : Reading input.'; d($Echo);
$Input = [
  'Id'      => Input('Id'),
  'Filter'  => Input('Filter'),
  'Payload' => Input('Payload'),
  // etc. according to API
];

// [5] Validate Input (Module Validator)
$Echo = 'API : Validating input.'; d($Echo);
$Errors = {ModuleName}Validator::{Action}($Input);
if( !empty($Errors) ){
  return JsonError('Validation Failed.', 422, $Errors);
}

// [6] Before Hooks
$Echo = 'API : Running Before Hooks.'; d($Echo);
{ModuleName}Hooks::Before{Action}($Input);

// [7] Call Service (Core Logic)
$Echo = 'API : Calling Service.'; d($Echo);
$Result = {ModuleName}Service::{Action}($Input);

// [8] After Hooks
$Echo = 'API : Running After Hooks.'; d($Echo);
{ModuleName}Hooks::After{Action}($Input, $Result);

// [9] Response Build
$Echo = 'API : Preparing response.'; d($Echo);
return JsonSuccess('Success', $Result);
```

Every AI-generated API should respect this **exact step order**.

---

## 5. API Naming Conventions

### 5.1 File Names

```text
{ModuleName}.Create.php   // Create new record
{ModuleName}.Update.php   // Update existing record
{ModuleName}.Delete.php   // Soft delete
{ModuleName}.View.php     // Single record fetch
{ModuleName}.List.php     // Multiple records list
{ModuleName}.Action.php   // Any custom action
```

Examples:

- `User.Create.php`
- `User.Login.php`
- `Wallet.Transfer.php`
- `Transaction.List.php`

### 5.2 Function Naming (Inside Services/Validators/Hooks)

- Service:
  - `{ModuleName}Service::Create()`
  - `{ModuleName}Service::Update()`
  - `{ModuleName}Service::Delete()`
  - `{ModuleName}Service::View()`
  - `{ModuleName}Service::List()`

- Validator:
  - `{ModuleName}Validator::Create()`
  - `{ModuleName}Validator::Update()`
  - etc.

- Hooks:
  - `{ModuleName}Hooks::BeforeCreate()`
  - `{ModuleName}Hooks::AfterCreate()`
  - etc.

---

## 6. Input Handling Rules

1. **Never use raw `$_GET` / `$_POST` / `$_REQUEST` directly**.  
2. Always use standard helpers:
   - `Input('Key')`
   - `InputJson()` for raw body JSON.
3. Validate:
   - Required fields
   - Types (string/int/float/bool/date)
   - Allowed values
   - Max lengths
4. On validation failure:
   - Do NOT continue
   - Return JSON with structured error list

Example error structure:

```json
{
  "status": "error",
  "message": "Validation Failed.",
  "errors": {
    "Amount": "Amount must be greater than 0",
    "UID": "UID is required"
  }
}
```

---

## 7. Output / Response Format

All APIs must return a **consistent JSON structure**.

### 7.1 Success Response

```json
{
  "status": "success",
  "message": "Operation successful.",
  "data": { ... },
  "meta": { ... } // optional: pagination, counts, etc.
}
```

### 7.2 Error Response

```json
{
  "status": "error",
  "message": "Short description of error.",
  "code": 403,
  "errors": { ... } // optional details
}
```

Internally, PHP helpers can be:

```php
JsonSuccess($Message, $Data = [], $Meta = []);
JsonError($Message, $Code = 400, $Errors = []);
```

---

## 8. Row Engine Usage in APIs

Direct DB access is **not allowed** in API files.

- API → calls **Service layer**
- Service layer → uses **Row engine**

Allowed pattern:

```php
$Row = Row('Wallet_Transaction')->Insert($Data);
```

Not allowed:

```php
$Q = "INSERT INTO Wallet_Transaction ...";
$DB->query($Q);
```

Row engine should handle:

- Correct DB select (MDBMS)
- Status-based soft delete
- Ecosystem/App/Tenant scope
- Audit fields (created_by, updated_by)

---

## 9. Security Rules for APIs

1. **Auth Required (by default)**  
   - Every API must check if user is logged-in (via `i`).
   - Public APIs must be explicitly marked and carefully validated.

2. **Permission Check**  
   - Map APIs to roles in:
     - `Config/permissions.json`
     - Or central Permission engine.

3. **Rate Limiting (Future)**  
   - API layer should support rate limiting via WebOS.Engine (future).

4. **No Sensitive Data in Logs**  
   - `$Echo` logs should not print full tokens, passwords, etc.

5. **CSRF & Session Rules (Web)**  
   - For browser-based APIs, CSRF tokens should be validated.

---

## 10. API Categories

### 10.1 CRUD APIs

Basic operations:
- Create
- Update
- Delete (soft)
- View
- List

### 10.2 Action APIs

Custom actions:
- `Wallet.Transfer`
- `User.ChangePassword`
- `KYC.Approve`

### 10.3 System APIs

Internal-only operations:
- Cron triggers
- System sync
- Cache warmup

### 10.4 Public / External APIs

For 3rd-party consumers:
- Require API key / token
- Use strict scopes
- Log all access

---

## 11. Example: Full Flow for `Wallet.Transfer` API

**File:**  
`App/Wallet/API/Wallet.Transfer.php`

**Steps:**

1. Boot + System + Kernel load.
2. Resolve current user (`i`).
3. Check permission: `wallet.transfer`.
4. Read input:
   - `FromWalletUID`
   - `ToWalletUID`
   - `Amount`
   - `Currency`
   - `Remark`
5. Validate input via `WalletValidator::Transfer()`.
6. Run `WalletHooks::BeforeTransfer()`.
7. Execute `WalletService::Transfer()`:
   - Use Row engine:
     - Deduct balance from source
     - Add balance to target
     - Insert transaction rows
8. Run `WalletHooks::AfterTransfer()`.
9. Return JSON success + transaction details.

---

## 12. AI Automation Rules (For AlienCyborg)

When AlienCyborg generates/modifies APIs:

- MUST:
  - Follow full lifecycle pattern
  - Respect naming conventions
  - Use Service, Validator, Hooks
  - Use Row engine only
  - Keep `$Echo` logs clean

- MUST NOT:
  - Touch Kernel code
  - Touch core WebOS/System engines
  - Bypass permission check
  - Hardcode sensitive secrets in code

AI should:
- Auto-scan existing APIs
- Learn patterns
- Keep consistency app-wide

---

## 13. Checklist for Any New API

Before considering an API “ready”:

- [ ] File in correct folder  
- [ ] Name follows `{Module}.{Action}.php`  
- [ ] Uses WebOS boot flow  
- [ ] Auth is checked  
- [ ] Permissions are checked  
- [ ] Input helper used (no direct `$_GET/$_POST`)  
- [ ] Validation via Validator class  
- [ ] Service layer handles DB  
- [ ] Hooks used where needed  
- [ ] Response is JSON (`JsonSuccess` / `JsonError`)  
- [ ] No sensitive logs  
- [ ] Documented in module/API docs  

---

## 14. Conclusion

This **Apps.API.Flow** standard ensures that every API in the Aliens WebOS ecosystem:

- Is predictable  
- Is secure  
- Is easy for AI + humans to extend  
- Fits naturally into AlienCyborg’s automation and development workflow  

For AlienCyborg (SoftwareEngineer role), this document is the **master reference** for how to design, generate, and refactor APIs inside any App.
