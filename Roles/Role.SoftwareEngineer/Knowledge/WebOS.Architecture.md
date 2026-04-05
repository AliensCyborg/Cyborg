# WebOS.Architecture — Detailed Version (Role.SoftwareEngineer)

## 1. Overview
**WebOS.Architecture.md** defines the *structural blueprint* of Aliens WebOS — how all folders, layers, engines, modules, apps, ecosystems, tenants, and services fit together.

Where:
- **WebOS.Master.md** explains WHAT WebOS is  
- **WebOS.Tech.md** explains HOW it operates technically  
- **WebOS.Architecture.md** explains HOW it is STRUCTURED  

This file ensures that AlienCyborg understands WebOS as a **full operating system**, not a framework.

This architecture must ALWAYS remain predictable so AI, humans, plugins, SDKs, servers, and automation tools can safely interact with it.

---

## 2. High-Level Architectural Diagram

```
                   ┌──────────────────────────────┐
                   │            Boot               │
                   └───────────────┬──────────────┘
                                   │
                   ┌───────────────▼──────────────┐
                   │            System              │
                   └───────────────┬──────────────┘
                                   │
                   ┌───────────────▼──────────────┐
                   │            Kernel              │
                   └───────────────┬──────────────┘
                                   │
        ┌──────────────────────────┼──────────────────────────┐
        │                          │                          │
┌───────▼───────┐        ┌─────────▼────────┐       ┌─────────▼────────┐
│      Unit      │        │        OS        │       │       Event       │
└───────┬───────┘        └─────────┬────────┘       └─────────┬────────┘
        │                           │                          │
        └───────────────────────────▼───────────────────────────┘
                                   App
```

---

## 3. Folder Architecture (Master-Level)

A WebOS ecosystem **must** use the following directory structure:

```
/Aliens
  ├── WebOS/
  │     ├── Boot/
  │     ├── System/
  │     ├── Kernel/
  │     ├── Unit/
  │     ├── OS/
  │     ├── Event/
  │     ├── App/
  │     └── Database/
  ├── SDK/
  ├── Apps/
  ├── Brand/
  └── Library/
```

### Roles:
- **WebOS** → The Operating System  
- **SDK** → Reusable features  
- **Apps** → UI + Client apps  
- **Brand** → Theme + CSS/JS overrides  
- **Library** → External 3rd-party code  

---

## 4. Boot Architecture

Boot is intentionally minimal.

Files:
```
Boot/os.php
Boot/shell.php
Boot/template.php
Boot/sql.php
Boot/import.php
```

Boot Responsibilities:
- Entry point  
- Detect ecosystem/app  
- Load System + Kernel  
- Prepare theme + template  
- No business logic  

Boot MUST NOT:
- Query DB directly  
- Load modules manually  
- Perform calculations  
- Contain mixed logic  

---

## 5. System Architecture

The System layer is the brain of WebOS.

Files include:
- index.php  
- 0.php  
- Row.php  
- WebOS_Row.php  
- Ecosystem.php  
- User.php  
- Theme.php  
- Module.php  
- App.php  
- Cache.php  
- UID.php  
- ABCD.php  
- Git.php  

Architectural rules:
- System = Fat Layer (core logic)
- All engines must load here
- No template rendering
- No API output
- No app-level logic
- All reusable logic lives here

System layer defines *WebOS capabilities*.

---

## 6. Kernel Architecture

Kernel = OS Security Shell.

Contains:
- protection scripts  
- access guards  
- version guards  
- anti-direct-access code  
- environment verification  

Rules:
- Kernel must remain tiny  
- Kernel must remain stable  
- Kernel must never be modified without global approval  
- Kernel protects the entire OS  

---

## 7. Unit Architecture

Units are shared mini-engines.

Examples:
- Notification unit  
- Logging unit  
- Analytics unit  
- SMS/Email units  

Rules:
- Units are reusable across apps  
- Units must depend on System layer, not App layer  
- Units cannot contain UI logic  

---

## 8. OS Architecture

OS folder contains:
- system utilities  
- helper scripts  
- admin-level tools  
- backup tooling  
- internal maintenance engines  

OS folder = “Command Line Tools” inside WebOS.

---

## 9. Event Architecture

Event folder contains:
- Cron jobs  
- Background tasks  
- Queue runners  
- Webhooks  
- System events  

Event rules:
- No UI  
- No templates  
- No heavy logic (delegated to Units/System)  

---

## 10. App Architecture

App is the *application layer* inside WebOS.

Each app has:
```
App/<AppName>/
   api/
   template/
   component/
   js/
   css/
   module/
```

App = Thin Layer  
System = Fat Layer  
Kernel = Secure Layer  

App Responsibilities:
- Render templates  
- Handle user interactions  
- Call System engines  
- Bind module logic  

App MUST NOT:
- Handle raw DB  
- Contain business logic  
- Break WebOS folder rules  

---

## 11. Module Architecture

Each module must follow strict structure:

```
ModuleName/
  ├── api/
  ├── template/
  ├── component/
  ├── js/
  ├── css/
  ├── sql/
  └── config.json
```

Modules act like “installed apps inside apps”.

Rules:
- All APIs use Row()  
- Templates contain only HTML  
- Components are reusable  
- JS/CSS separated  
- config.json defines module properties  

---

## 12. Brand & Theme Architecture

Brand → overrides for multiple apps.  
Theme → layout + color + structure.

Brand folder architecture example:

```
Brand/ThemeName/
   css/
   js/
   layout/
   component/
   template/
   config.json
```

Brand rules:
- Can override templates  
- Can override components  
- Can override CSS/JS  
- CANNOT override Kernel/System  

---

## 13. Database Architecture (MDBMS)

WebOS supports multiple DBs:
- ecosystem DB  
- tenant DB  
- monthly partition DB  
- archive DB  

Architecture:
```
Primary DB
│
├── Ecosystem DB
├── Tenant DBs
└── Partitioned DBs (month/year)
```

All access must go through:
- `Row()`
- `WebOS_Row`

DB design rules:
- No hard deletes  
- Status-based lifecycle  
- Strong indexing  
- Partition-based scaling  

---

## 14. Routing Architecture

Routing inside WebOS is:
- brand-aware  
- ecosystem-aware  
- tenant-aware  
- app-aware  

Routing flow:
```
Request → Boot → Ecosystem → App → Module → Template
```

APIs:
```
Request → Boot → App/API → System → Row() → Response
```

---

## 15. Multi-Tenant Architecture

Tenant support is built at:
- Ecosystem level  
- DB level  
- Module level  
- App level  

Tenant-specific:
- Branding  
- Modules  
- Limits  
- Features  

All tenant rules must be DB-driven and MUST NOT require code changes.

---

## 16. Cross-Language Architecture (WebOS JS, WebOS PY)

WebOS architecture exists in:
- PHP → Main OS  
- JS → Frontend OS  
- Python → AppOS  

All three must mirror:
- Boot/System/Kernel layering  
- Engine naming  
- Coding style  
- Module/Component patterns  

---

## 17. Security Architecture

Security enforced by:
- Kernel layer  
- UID encoding rules  
- Access() function  
- DB-level tenant isolation  
- No direct file execution  
- No direct SQL  
- API security wrappers  

---

## 18. Deployment Architecture

Deployment works through:
- GitHub Actions  
- A0 development server  
- A1/A2/A3 production servers  
- Automatic syncing  
- Version-based structure  
- Artifact bundles  

---

## 19. Final Architectural Laws (Cyborg Permanent Memory)

Cyborg must follow:

1. Boot → System → Kernel → Unit → OS → Event → App → DB  
2. App is THIN  
3. System is FAT  
4. Kernel is LOCKED  
5. Row() is mandatory  
6. Templates contain 0 PHP logic  
7. Modules must follow strict folder structure  
8. Brand overrides must be clean  
9. Tenant logic must be DB-driven  
10. Architecture must remain PREDICTABLE  

This file MUST stay internalized in the Cyborg’s engineering brain.

