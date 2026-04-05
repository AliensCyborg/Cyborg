# Apps.NewApp.Flow — Detailed Version

This document defines the **end‑to‑end flow** for creating a **new App** inside the Aliens WebOS ecosystem, from planning to production, specifically for the  
`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Apps` layer.

This is the **master playbook** that AlienCyborg (SoftwareEngineer role) and human developers must follow whenever a new App is created.

---

## 1. Purpose of This Standard

Why yeh document important hai:

- Har new App ek **fixed, predictable pattern** follow kare.  
- WebOS + Ecosystem + Brand + Modules + APIs sab **sync** me rahein.  
- AI (AlienCyborg) ko clear roadmap mile:
  - Kaunse steps automate karne hain
  - Kaunse files generate karni hain
  - Kaunse tables / configs touch karne hain

Goal:
- New App banana = **assembly process**, na ki “random coding”.

---

## 2. High‑Level New App Flow (Bird’s Eye View)

End‑to‑end flow simple language me:

```text
1. App Idea + Scope clear karo
2. Database & Ecosystem planning
3. App registration (DB & config)
4. Folder structure create karo
5. Brand + Theme mapping set karo
6. Core Modules define + skeleton create karo
7. Screens + Templates + Components link karo
8. API layer define + implement karo
9. Menu / Routes / Access control define karo
10. Test + Seed + Demo data create karo
11. Deploy on correct Ecosystem + Brand
12. AI automation hooks setup karo
```

AlienCyborg ko har step ka **exact expected output** pata hona chahiye.

---

## 3. Prerequisites Before Creating a New App

New App start karne se pehle:

- Ecosystem selected:
  - Example: `Aliens`, `Aeldo`, `Coinpods`, etc.
- Brand strategy decided:
  - Ek brand ya multi‑brand?
- Tenancy model clear:
  - Single tenant app?
  - Multi‑tenant SaaS app?
- Core business modules list ready:
  - User, Wallet, Transaction, KYC, etc.

AlienCyborg should **always ask / confirm**:

- App ka **primary purpose**  
- Target **users** (Admin, End‑User, Partners)  
- Expected **scalability** (users, transactions, tenants)  

---

## 4. App Naming & Slug Rules

### 4.1 Naming

- App Name:
  - Human friendly: `Coinpods Exchange`, `Aeldo Shop`, `HX Wallet`
- App Code / Slug:
  - Short, machine‑friendly, lowercase:
    - `exchange`
    - `shop`
    - `wallet`
    - `mlm`
    - `erp`

### 4.2 Constraints

- No spaces in slug
- Only `[a-z0-9_-]`
- Slug must be **unique** within WebOS instance

Examples:

```text
App Name : Coinpods Exchange
App Slug : exchange

App Name : Aeldo Marketplace
App Slug : shop

App Name : HX Wallet
App Slug : wallet
```

---

## 5. Database & Ecosystem Registration Flow

New App ko **DB me register karna** must hai. Typical flow:

1. **App master table** me entry:
   - `app_code` (slug)
   - `app_name`
   - `ecosystem_id`
   - `status` (1 = active)
   - `default_theme_id`
   - `is_multi_tenant` (0/1)

2. **Ecosystem ↔ App linking**:
   - Ecosystem table me mapping:
     - Kaunse apps is ecosystem me allowed hain
     - Default home app for ecosystem

3. **Tenant mapping** (if multi‑tenant):
   - Tenant table / mapping table:
     - `tenant_id`
     - `app_code`
     - `features_enabled`
     - `limits`
     - `branding_overrides`

AlienCyborg ke liye rule:

- New App banate waqt:
  - Always generate **DB insert/seed script** for App registration.
  - Always define **minimal mapping**:
    - Ecosystem ↔ App
    - App ↔ Theme

---

## 6. Folder Structure for a New App

Standard path:

```text
App/{AppCode}/
  ├── index.php
  ├── API/
  ├── Modules/
  ├── Screens/
  ├── Layout/
  ├── Config/
  ├── Assets/
  └── Tests/
```

### 6.1 index.php

Entry / router file for App:

- Receives requests specific to this app
- Resolves:
  - Module
  - Screen
  - Action
- Uses WebOS System & Kernel (no custom mini‑frameworks)

### 6.2 API/

Contains all API endpoints of this App. Follows `Apps.API.Flow` standard:

```text
App/{AppCode}/API/
  ├── index.php
  ├── User.Login.php
  ├── User.Register.php
  ├── Wallet.Transfer.php
  ├── Transaction.List.php
  └── ...
```

### 6.3 Modules/

Contains business modules as per `Apps.Modules` rules:

```text
App/{AppCode}/Modules/{ModuleName}/
  ├── Logic.php
  ├── Validation.php
  ├── Service.php
  ├── Hooks.php
  ├── Config.json
  ├── Screens/
  ├── API/
  └── Tests/
```

### 6.4 Screens/

App‑level screens (generic, cross‑module combos):

```text
App/{AppCode}/Screens/
  ├── Dashboard.php
  ├── Reports.php
  ├── Settings.php
  └── ...
```

### 6.5 Layout/

Layout & page structure:

```text
App/{AppCode}/Layout/
  ├── Master.php
  ├── Header.php
  ├── Footer.php
  ├── Sidebar.php
  └── ...
```

### 6.6 Config/

Config files:

```text
App/{AppCode}/Config/
  ├── manifest.json
  ├── permissions.json
  ├── routes.json
  ├── menu.json
  └── settings.json
```

### 6.7 Assets/

App‑specific assets:

```text
App/{AppCode}/Assets/
  ├── css/
  ├── js/
  ├── images/
  └── icons/
```

---

## 7. Manifest & Config Design

### 7.1 manifest.json

Defines base identity of app:

```json
{
  "code": "exchange",
  "name": "Coinpods Exchange",
  "version": "1.0.0",
  "status": 1,
  "multi_tenant": true,
  "default_theme": "cp_default",
  "description": "Enterprise-grade crypto exchange app running on Aliens WebOS."
}
```

### 7.2 permissions.json

Maps roles to allowed actions:

```json
{
  "admin": {
    "modules": ["User", "Wallet", "Transaction", "KYC"],
    "permissions": ["*"]
  },
  "user": {
    "modules": ["Wallet", "Transaction"],
    "permissions": ["view", "create"]
  }
}
```

### 7.3 routes.json

Defines URL → handler mapping:

```json
{
  "/": {
    "type": "screen",
    "screen": "Dashboard"
  },
  "/wallet": {
    "type": "module.screen",
    "module": "Wallet",
    "screen": "List"
  },
  "/transaction/list": {
    "type": "module.screen",
    "module": "Transaction",
    "screen": "List"
  }
}
```

### 7.4 menu.json

Defines sidebar/top menu:

```json
[
  {
    "title": "Dashboard",
    "icon": "home",
    "route": "/"
  },
  {
    "title": "Wallets",
    "icon": "wallet",
    "route": "/wallet"
  },
  {
    "title": "Transactions",
    "icon": "transactions",
    "route": "/transaction/list"
  }
]
```

---

## 8. New App Creation — Detailed Step‑by‑Step Flow

### Step 1: Define App Blueprint

- Write small spec:
  - App Name, Slug
  - Ecosystem
  - Target roles
  - Core modules list

AlienCyborg should be able to **generate this blueprint** from a simple prompt.

---

### Step 2: Register App in DB

- Generate SQL/Row‑based script:
  - Insert into `App` master table
  - Link to Ecosystem
  - Set default theme

Example (conceptual):

```php
Row('App')->Insert([
  'code'          => 'exchange',
  'name'          => 'Coinpods Exchange',
  'ecosystem_id'  => 1,
  'status'        => 1,
  'multi_tenant'  => 1
]);
```

---

### Step 3: Create App Folder Structure

Generate folder tree under `App/{AppCode}/` as defined in Section 6.

AI automation (AlienCyborg) should:

- Propose full tree
- Generate empty/skeleton files with boilerplate

---

### Step 4: Setup Configs (manifest, routes, menu, permissions)

- Create:
  - `Config/manifest.json`
  - `Config/routes.json`
  - `Config/menu.json`
  - `Config/permissions.json`

- Fill with **minimal working data**.

---

### Step 5: Create Core Modules

For each module in app blueprint:

- Create module skeleton:
  - `Logic.php`
  - `Validation.php`
  - `Service.php`
  - `Hooks.php`
  - `Config.json`
  - `Screens/List,Add,Edit,View.php`
  - `API/Create,Update,List,View,Delete.php`

AlienCyborg should use **Apps.Modules** + **Apps.API.Flow** standard.

---

### Step 6: Wire Screens + Layout + Theme

- Link:
  - App Screens with Layout
  - Layout with Brand Theme

Rules:

- No direct inline CSS/JS:
  - Use Brand/Theme assets
- Use WebOS template engine standards.

---

### Step 7: Implement APIs

For each module action:

- Create proper API file using Apps.API.Flow:

  - Auth check
  - Permission check
  - Input handling
  - Validation
  - Service call
  - Hooks
  - JSON response

---

### Step 8: Seed Data & Demo Flow

- Optional but recommended:
  - Create basic seed data for:
    - Menus
    - Demo users
    - Demo wallets/transactions (if relevant)

This helps:

- QA team
- Demos
- Automated tests

---

### Step 9: Testing

- Smoke tests:
  - Can we login?
  - Dashboard loads?
  - Core module flows work?

- Module tests:
  - Unit tests in each module
  - API integration tests

---

### Step 10: Deployment & Environment Binding

- Bind App to:
  - Specific ecosystems
  - Specific domains/subdomains (if needed)
- Configure:
  - CDN
  - Backups
  - MDBMS mapping (if heavy data)

---

### Step 11: AI Hooks & Automation

For AlienCyborg:

- Maintain mapping of:
  - App → Modules
  - App → APIs
  - App → Routes
- Use this mapping to:
  - Auto‑propose new modules
  - Auto‑generate APIs
  - Auto‑update docs

---

## 9. Safety & Consistency Rules

1. **Never create App with custom random structure**  
   Always follow sections 6–8.

2. **Never bypass Row engine**  
   All DB write/read through proper services.

3. **Never hardcode ecosystems/tenants inside App code**  
   Use standard Ecosystem + Tenant mapping.

4. **Never mix brand logic with App logic**  
   Brand = visuals; App = business.

---

## 10. New App Checklist (Quick Use)

Before marking a new App as “ready for dev team”:

- [ ] App Name + Slug finalized  
- [ ] Ecosystem mapping done  
- [ ] App registered in DB  
- [ ] App folder created under `App/{AppCode}/`  
- [ ] Config files (manifest, routes, menu, permissions) created  
- [ ] Core modules list created + skeletons generated  
- [ ] Layout + Screens wired with Theme  
- [ ] Minimum critical APIs ready  
- [ ] Basic seed data (optional but helpful)  
- [ ] App documented (short README)  
- [ ] AI (AlienCyborg) updated mapping for this App  

---

## 11. Conclusion

`Apps.NewApp.Flow` ensures that:

- New Apps are born **clean, structured and WebOS‑compatible**  
- Human + AI collaboration becomes extremely fast  
- Upgrades, refactors, and scaling remain safe  

For AlienCyborg (SoftwareEngineer role), this document acts as the **step‑by‑step factory manual** for manufacturing new Apps inside the Aliens WebOS ecosystem.
