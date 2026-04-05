# WebOS.Master — Detailed Version (Role.SoftwareEngineer)

## 1. Overview
WebOS.Master.md is the **root knowledge file** that teaches the Cyborg how the entire **Aliens WebOS Operating System** works at a conceptual, architectural, and execution level.

It serves as:
- The *master blueprint* of WebOS  
- The *highest-level explanation* of all layers  
- A *central reference* for System, Kernel, Boot, Unit, OS, Event, App, Brand, Theme, Module, and MDBMS logic  
- A *permanent knowledge foundation* for the SoftwareEngineer role of AlienCyborg

This document must always be loaded before:
- WebOS.Tech  
- WebOS.Architecture  
- Row Engine docs  
- Module structure docs  
- Ecosystem/App/Tenant docs  

---

## 2. What is Aliens WebOS?

**Aliens WebOS** is a multi-tenant, multi-app, enterprise-grade **Operating System for Web Applications** built in PHP with cross-language equivalents (JS/Python).

It is not a normal framework.  
It is a full **software operating system** that provides:

- Boot layer  
- Kernel layer  
- System engines  
- App manager  
- Module manager  
- Theme/Brand engine  
- Ecosystem manager  
- User & Identity engine  
- Row() + MDBMS  
- Template engine  
- Component engine  
- Event engine  
- DevOps hooks  

WebOS ensures:
- Consistency  
- Speed  
- Zero-duplication  
- Predictability  
- AI-friendly patterns  
- Company-wide standards  
- Enterprise-level scalability  

---

## 3. The WebOS Layer Model (Master Level)

```
Boot
System
Kernel
Unit
OS
Event
App
Database
```

Each layer has strict rules:

### 3.1 Boot Layer
- Entry point  
- Loads core system  
- Detects ecosystem/app  
- Loads manifest/theme  
- Does **NOT** contain business logic  

### 3.2 System Layer
The **heart** of WebOS.

Contains engines:
- Row Engine  
- WebOS_Row  
- Cache  
- Ecosystem  
- User  
- UID  
- Git  
- Module  
- Theme  
- App Engine  

System Layer = FATTest layer.  
All reusable logic lives here.

### 3.3 Kernel Layer
- Small  
- Secure  
- Immutable  
- Checks access  
- Ensures environment safety  

Kernel = BIOS of WebOS.

### 3.4 Unit Layer
Reusable mini-engines:
- Logging  
- Analytics  
- Notifications  
- Utility engines  

### 3.5 OS Layer
System-level utilities:
- File operations  
- Backup manager  
- Server checks  

### 3.6 Event Layer
- Cron  
- Webhooks  
- App events  
- Business events  

### 3.7 App Layer
Thin application logic:
- Templates  
- APIs  
- Modules  
- Components  
- Pages  

App layer controls UI + orchestration.

### 3.8 Database Layer
- Constant source of truth  
- Multi-DB  
- Tenant-aware  
- Ecosystem-aware  
- Status-based lifecycle  

---

## 4. WebOS Engines — Master Description

### 4.1 Row Engine (Mandatory AI Knowledge)
Row() handles:
- CRUD  
- Soft delete  
- DB selection  
- Logging  
- Index safety  
- MDBMS logic  

Never bypass Row().  
Never write raw SQL inside apps.

### 4.2 WebOS_Row
Wrapper for Row() with:
- Audit fields  
- Tenant injection  
- Ecosystem injection  
- App context  

### 4.3 User Engine
Handles:
- Login  
- Session  
- Permissions  
- Role mapping  
- Identity constant `i`  

### 4.4 Ecosystem Engine
Resolves:
- Brand  
- Theme  
- App selection  
- DB prefix  
- Multi-app routing  

### 4.5 Module Engine
Each module has:
- api/  
- template/  
- component/  
- css/  
- js/  
- sql/  

Module engine ensures:
- Correct loading  
- Version compatibility  
- Isolation  

### 4.6 Theme Engine
Controls:
- Layout  
- CSS bundle  
- JS bundle  
- Brand overrides  
- UI wiring  

### 4.7 Cache Engine
Caches:
- Menu  
- App metadata  
- Ecosystem config  
- Tenant config  
- Module list  

### 4.8 Git Engine
Tracks:
- Version  
- Branch  
- Updates  

### 4.9 UID Engine
Safe encoding rules for:
- user UID  
- order UID  
- transaction UID  

---

## 5. WebOS File Structure Rules

Every WebOS installation must follow:

```
Boot/
System/
Kernel/
Unit/
OS/
Event/
Database/
App/
```

Strict rules:
- Never delete System or Kernel files  
- Never modify Kernel  
- System Layer changes must be approved  
- App files may be edited freely (templates, modules, components)  

---

## 6. WebOS Request Lifecycle

### Step-by-Step:

1. Browser hits entry  
2. Boot loads System  
3. System loads:
   - Ecosystem  
   - App  
   - User (i)  
   - Theme  
4. Kernel validates environment  
5. Module/Unit selected  
6. Templates/components loaded  
7. API or Page rendered  
8. Response sent  

---

## 7. Ecosystem → App → Tenant Logic

WebOS MUST always detect:

1. Ecosystem (brand-level container)
2. App (functional product)
3. Tenant (SaaS customer)
4. User (current i)
5. Role (authorization)
6. Module (function logic)
7. Theme (UI styling)

This hierarchy allows:
- 1 installation → 1000 apps  
- 1 app → 10,000 tenants  
- 1 tenant → independent branding  
- 1 ecosystem → thousands of apps  

---

## 8. WebOS Standards for DB (Master Rules)

### 8.1 Use Row()  
No direct SQL allowed.

### 8.2 Status-based model
All tables must include:
```
status
created_at
updated_at
```

### 8.3 Index rules
Indexes required on:
- uid  
- foreign keys  
- status  
- created_at  

### 8.4 Multi-Database management
WebOS automatically:
- Splits tables  
- Routes queries  
- Reduces heavy loads  

---

## 9. WebOS Standards for Module Development

Cyborg must enforce:
- PascalCase everywhere  
- 2-space indentation  
- Clean folder boundaries  
- No mixed responsibilities  
- API must use Row()  
- Templates must not contain PHP logic  
- Components must be reusable  

Correct module template:

```
ModuleName/
  api/
    ApiName.php
  component/
    Box.php
  template/
    Page.php
  css/
    style.css
  js/
    script.js
  sql/
    structure.sql
  config.json
```

---

## 10. WebOS Security Master Rules

- Kernel must never be modified  
- Raw SQL must never be exposed  
- Sensitive keys must never be in App folder  
- Use UID instead of exposing IDs  
- Tenant isolation enforced through DB rules  

---

## 11. WebOS Versioning Philosophy

Version tag format:

```
PHP.25.11.xx
JS.25.11.xx
PY.25.11.xx
```

Rules:
- Main branch always stable  
- Dev branch feature development  
- Hotfix branch immediate fixes  

---

## 12. Cyborg Behaviour for WebOS Tasks

WebOS.Master instructs Cyborg to ALWAYS:
- Think in layers  
- Use correct engines  
- Respect system-first logic  
- Maintain stability  
- Produce predictable output  
- Follow AliensStyle  
- Maintain tenant-safe actions  
- Respect multi-DB routing  
- Preserve backward compatibility  

---

## 13. Final WebOS Blueprint (Cyborg Brain Imprint)

Cyborg must permanently internalize:

- WebOS = Operating System  
- Boot = Entry  
- System = Engines  
- Kernel = Security  
- Unit = Reusable logic  
- OS = System tools  
- Event = Triggers  
- App = UI + API  
- Database = Multi-DB backbone  

And Cyborg must follow:
- No raw SQL  
- No direct Kernel edits  
- Always AliensStyle  
- Always Row Engine  
- Always ecosystem-aware  
- Always tenant-aware  
- Always clean structure  
- Always predictable behaviour  

This is the **Master Knowledge of WebOS** for the AlienCyborg SoftwareEngineer Role.
