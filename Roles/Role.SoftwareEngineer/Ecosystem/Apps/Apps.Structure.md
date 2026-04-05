# Apps.Structure — Detailed Version

This document defines the complete structural, architectural, and operational blueprint for how **Applications (Apps)** function inside the AlienCyborg > Role.SoftwareEngineer ecosystem, aligned with Aliens WebOS philosophy.

## 1. Purpose of This Document
- Provide a **clear standard** for how apps are organized.
- Ensure **AI automation** and **developers** follow the same folder and file conventions.
- Define **components, flows, lifecycle, communication**, and **integration rules** for apps.

## 2. What is an App in Aliens WebOS
An App is a **complete functional product** running on top of WebOS’s system layer.

Examples:
- Wallet App  
- Admin Console  
- Analytics App  
- Exchange App  

Each App is:
- A **container of modules**
- A **UI + API system**
- A **runtime environment** with its own logic
- A **tenant-aware** and **ecosystem-aware** unit

Apps MUST follow strict directory structure rules.

---

## 3. Core App Folder Structure

```
App/
 └── {AppName}/
       ├── Config/
       │     ├── manifest.json
       │     ├── modules.json
       │     └── permissions.json
       │
       ├── API/
       │     ├── {Module}.Create.php
       │     ├── {Module}.Update.php
       │     ├── {Module}.List.php
       │     ├── {Module}.View.php
       │     └── index.php
       │
       ├── Screens/
       │     ├── Home.php
       │     ├── Dashboard.php
       │     ├── {Module}/
       │     │      ├── List.php
       │     │      ├── Add.php
       │     │      ├── Edit.php
       │     │      └── View.php
       │
       ├── Components/
       │     ├── Tables/
       │     ├── Cards/
       │     └── Widgets/
       │
       ├── Modules/
       │     └── {Module}/
       │             ├── Logic.php
       │             ├── Validation.php
       │             ├── Service.php
       │             └── Hooks.php
       │
       ├── Hooks/
       │     ├── OnAppBoot.php
       │     ├── OnModuleLoad.php
       │     └── OnUserLogin.php
       │
       ├── Assets/
       │     ├── css/
       │     ├── js/
       │     ├── icons/
       │     └── images/
       │
       └── index.php
```

---

## 4. App Lifecycle (Step-by-Step)

### Step 1: WebOS Boot Layer Detects Current App
Using:
- Domain
- Route
- Ecosystem settings
- Tenant mapping

### Step 2: App Manifest is Loaded
`Config/manifest.json` defines:
- App Name  
- Version  
- Required modules  
- Enabled features  
- Theme mapping  

### Step 3: Modules Are Mounted
For each module:
- Load module folder
- Register routes
- Register hooks
- Load permissions

### Step 4: User Permissions Load
Permissions file:
`Config/permissions.json`

Loaded into:
- Role engine
- Menu engine
- UI visibility engine

### Step 5: UI Rendering
`Screens/` folder defines all page screens using template + theme.

Step-by-step:
1. Resolve screen  
2. Import layout  
3. Load components  
4. Merge dynamic data  
5. Render final HTML  

### Step 6: API Execution
All app APIs inside:
```
App/{App}/API/
```
run using WebOS:
- Auth engine  
- Row engine  
- Cache engine  
- MDBMS engine  
- Response engine  

### Step 7: Hooks & Automation Trigger
Examples:
- OnAppBoot  
- OnModuleLoad  
- OnUserLogin  

They modify:
- Menus  
- UI  
- Data preload  
- Caching rules  

---

## 5. App Types in WebOS

### 1. System Apps
Deeply integrated.  
Examples:
- WebOS Console  
- User Management  

### 2. Business Apps
Used by tenants.  
Examples: Wallet, Exchange, CRM  

### 3. Utility Apps
Small focused tools.

### 4. AI Apps (AlienCyborg internal)
Automation tools.

---

## 6. Module Structure

```
Logic.php
Validation.php
Service.php
Hooks.php
Screens/
API/
```

Rules:
- NO direct SQL  
- NO logic in Screens  
- Always use Row engine  

---

## 7. App Security Rules
- Role-based gating  
- Permission JSON required  
- No direct $_GET/$_POST  
- Only Input() helpers  
- CDN for assets  
- Hooks must not bypass Kernel  

---

## 8. Developer Rules
- Use components  
- Use modules  
- Use manifest  
- Avoid duplication  

---

## 9. App Manifest Format

```
{
  "name": "Wallet",
  "slug": "wallet",
  "version": "1.0.0",
  "modules": ["transactions"],
  "theme": "default"
}
```

---

## 10. Inter-App Communication

```
App('Analytics')->Module('Stats')->Run('Recalculate');
```

---

## 11. AI Automation (Inside AlienCyborg)

AI can:
- Generate apps  
- Generate modules  
- Create screens  
- Fill manifests  
- Validate structure  

Safety:
- No Kernel edits  
- No System edits  

---

## 12. App Removal Rules
- Disable mapping  
- Remove modules  
- Remove screens  
- Remove APIs  

---

## 13. Future-Proofing
- Version every app  
- Avoid coupling  
- Follow module boundaries  

---

## 14. Conclusion
This structure ensures enterprise-grade architecture and AI‑friendly automation.

