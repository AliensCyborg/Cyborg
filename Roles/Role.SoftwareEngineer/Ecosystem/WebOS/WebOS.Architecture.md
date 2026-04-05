# WebOS.Architecture — Role.SoftwareEngineer Ecosystem
(Detailed Version)

## 1. Introduction
This document provides the deepest, most detailed breakdown of the **Aliens WebOS Architecture** specifically for the **Role.SoftwareEngineer** inside the AlienCyborg Agent System.  
It explains **why** WebOS is structured the way it is, **how** each layer behaves, and **what rules AI + human engineers must follow** when modifying or extending it.

---

## 2. Core Purpose of WebOS Architecture
- Create a **unified Operating System for Apps**  
- Allow **multiple ecosystems**, **multiple apps**, **multiple brands**, and **multiple tenants** to run from a single installation  
- Provide **standardization** across Aliens Company projects  
- Make systems **AI-extensible**  
- Reduce code duplication to zero  
- Ensure high scalability and clean expansion  

---

## 3. Architectural Layers (Top-Level Overview)

WebOS is built on strictly separated layers:

```
Boot Layer
System Layer
Kernel Layer
Unit Layer
OS Layer
Event Layer
Database Layer
App Layer
```

The strict separation ensures predictable behavior.

**Dependency Direction (MUST NEVER BREAK):**

```
Boot → System → Kernel → Unit → OS → Event → App
```

An App can use System, but System must never rely on App.

---

## 4. Boot Layer (Root of WebOS Execution)

### 4.1 Responsibilities
- Start the OS  
- Load system  
- Configure environment  
- Route control to System and Kernel  
- Initialize global constants and paths  

### 4.2 Important Files
- `Boot/os.php`
- `Boot/shell.php`
- `Boot/template.php`
- `Boot/sql.php`

### 4.3 Rules
- Boot must be extremely minimal  
- No business logic  
- No app logic  
- Only “prepare and handover” role  

---

## 5. System Layer (The Brain of WebOS)

The **System Layer** is the most important part of WebOS.

### 5.1 Purpose
- Provide reusable engines  
- Act as a universal service layer  
- Handle DB, cache, ecosystems, themes, modules, identity  

### 5.2 Engines inside System
- **Row Engine**  
- **WebOS_Row Engine**  
- **Cache Engine**  
- **Ecosystem Engine**  
- **Module Engine**  
- **App Engine**  
- **Theme Engine**  
- **User Engine**  
- **UID Engine**  
- **Git Engine**  
- **Utility Engine** (ABCD.php)

### 5.3 Rules
- System layer logic must be stable and universal  
- Never place app-specific logic here  
- System must remain upgrade-safe for all apps  

---

## 6. Kernel Layer (The Secure Core)

### 6.1 Purpose
- Enforce security  
- Sanity checks  
- OS-level validations  
- Protect System and App layers from unauthorized access  

### 6.2 Rules
- Kernel code must rarely change  
- No business logic  
- No direct DB queries  
- Must prevent direct file access  

### 6.3 Example Security Enforcement
- Check ecosystem validity  
- Check app permissions  
- Check session/user authentication  
- Block invalid module loading  

---

## 7. Unit Layer (Reusable Functional Blocks)

### 7.1 Purpose
- Contain reusable “functional modules”  
- Provide mid-level logic (neither system-level nor app-level)

### 7.2 Examples
- Notification Unit  
- Analytics Unit  
- Wallet Engine Unit  
- UserMetadata Engine  

### 7.3 Rules
- Units may depend on System  
- Units must not depend on Apps  
- Use for reusable patterns  

---

## 8. OS Layer (System Tools / Internal Services)

### 8.1 Purpose
- OS-like tools inside WebOS  
- Maintenance operations  
- CLI commands  
- Health checks  

### 8.2 Examples
- System/SystemCheck  
- OS/TaskRunner  
- OS/DevTools  

---

## 9. Event Layer (Triggers and Automations)

### 9.1 Purpose
- Handle webhooks  
- Handle cron jobs  
- Triggered processes (background tasks)  

### 9.2 Examples
- Cron/Event  
- Webhook Event  
- Payment Gateway Event Handlers  

---

## 10. Database Layer (Architecture of Data)

### 10.1 MDBMS (Multi-Database Management System)
WebOS can:
- Split data per ecosystem
- Split data per tenant
- Split data per month/year (partitioning)

### 10.2 Row Engine Requirements
- No raw SQL in Apps  
- Must log all DB operations  
- Must support soft delete  
- Must auto-select DB based on rules  

### 10.3 Naming Rules
```
prefix_ecosystem_table
prefix_app_table
prefix_module_table
```

### 10.4 CRUD Philosophy
- Use Row(), WebOS_Row only  
- Respect status fields  
- Avoid direct delete  

---

## 11. App Layer (The Frontline of WebOS)

### 11.1 Purpose
Apps are the actual products like:
- Wallet  
- Exchange  
- Admin Panel  
- Aeldo Shop  
- CRM  
- ERP  
- LMS  
- etc.

### 11.2 App Contains
- Modules  
- Templates  
- Components  
- CSS/JS  
- App-level APIs  
- App-level events  
- App manifest  

### 11.3 App Rules
- Apps cannot override System or Kernel  
- Apps must follow AliensStyle  
- Apps must remain compatible with DB rules  

---

## 12. Themes (Brand + UI Architecture)

### 12.1 Purpose
- Provide visual identity  
- Change entire UI instantly  
- Support multi-brand design  

### 12.2 Theme Responsibilities
- Colors  
- Fonts  
- Layout structure  
- Header/footer/sidebar  
- Responsive behavior  

---

## 13. Module Architecture (Heart of App Development)

A module is a complete mini-ecosystem inside the app.

### 13.1 Module Contains
- API  
- Template  
- Component  
- CSS  
- JS  
- Events  
- Logic  

### 13.2 Module Rules
- Must follow WebOS module naming  
- Must use Row API  
- Must be compatible with themes  
- Must not break other modules  

---

## 14. WebOS Constants

### 14.1 i — Logged-In User
### 14.2 ecosystem — Current ecosystem
### 14.3 app — Current app
### 14.4 manifest — Current app manifest
### 14.5 aliens — Core company data
### 14.6 webos — OS-level configuration

---

## 15. Full Request Flow (Deep)

```
Browser Request
↓
WebServer Routing
↓
Boot Layer Initialization
↓
System Layer Loading
↓
Kernel Security Validation
↓
Ecosystem Resolution
↓
App Resolution
↓
Module Routing
↓
Unit Execution
↓
Theme Rendering
↓
Output Response
```

---

## 16. AI Safety Rules for Editing WebOS

- Never touch Kernel unless explicitly required  
- Avoid editing System engines unless ordered  
- Use Unit or App for new logic  
- Never bypass Row Engine  
- Never break soft-delete logic  
- Preserve AliensStyle in all code  

---

## 17. Extension Strategy

### Add a New App:
- Add folder in App/
- Insert manifest
- Register in DB
- Use System engines

### Add a New Module:
- Create module skeleton
- Register module
- Add DB tables (if needed)

### Add a New Engine:
- Create System file
- Register in System/index.php

---

## 18. Versioning Architecture

### Example:
```
PHP.25.11.00
PHP.25.12.00
PHP.26.00.00
```

Each major change increments:
- Boot flow  
- System engines  
- New modules  
- Improved architecture  

---

## 19. End of Document
This file gives the complete technical architecture of WebOS for professional internal use inside AlienCyborg Role.SoftwareEngineer.

