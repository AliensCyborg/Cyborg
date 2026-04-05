# WebOS.Layers — Role.SoftwareEngineer Ecosystem  
(Detailed Version)

## 1. Introduction
This document explains **all execution layers inside Aliens WebOS**.  
It is written for the **Role.SoftwareEngineer** version of AlienCyborg to ensure correct understanding and predictable engineering decisions.

---

## 2. Why WebOS Uses Layers
WebOS is an enterprise‑grade multi‑ecosystem, multi‑tenant, multi‑app operating system.

Layers provide:
- Clean separation of responsibilities  
- Predictable behaviour  
- Zero-conflict architecture  
- Upgrade‑safe development  
- AI‑safe engineering boundaries  
- Multi‑stack (PHP/JS/Python/Dart) compatibility  

---

## 3. The 8 Official Layers of WebOS  
These layers are **strict** and must never be mixed.

```
1. Boot  
2. System  
3. Kernel  
4. Unit  
5. OS  
6. Event  
7. Database  
8. App  
```

Dependency direction must always remain:

```
Boot → System → Kernel → Unit → OS → Event → App
```

---

## 4. Layer 1 — Boot Layer

### Purpose  
The starting point of every WebOS request.  
It sets the environment and loads System + Kernel.

### Responsibilities  
- Load OS entry  
- Register constants  
- Detect environment  
- Load System index  
- Load Kernel security  
- Activate template renderer  
- Register ecosystem/app context  

### Key Files  
- `Boot/os.php`  
- `Boot/shell.php`  
- `Boot/template.php`  
- `Boot/sql.php`  

### Rules  
- No business logic  
- Only initialization  
- Never bypass System  

---

## 5. Layer 2 — System Layer

### Purpose  
The **brain of WebOS**.  
All core engines live here.

### Engines Inside System  
- Row Engine  
- WebOS_Row  
- Ecosystem Engine  
- User Engine  
- UID Engine  
- Module Engine  
- App Engine  
- Theme Engine  
- Cache Engine  
- Git Engine  
- Utility Engine (ABCD.php)  

### Responsibilities  
- Provide universal services  
- Shared by all apps  
- Shared by Kernel/Unit/App layers  

### Rules  
- Never add app-specific logic here  
- Must remain stable across versions  
- Core engineering layer — highest importance  

---

## 6. Layer 3 — Kernel Layer

### Purpose  
The **OS kernel of WebOS**.  
Handles critical security and validation.

### Responsibilities  
- Enforce access rules  
- Protect system layers  
- Validate ecosystem + app  
- Sanity checks  
- Prevent direct file access  

### Rules  
- Do not modify without explicit permission  
- No business logic allowed  
- Pure security + OS internals  

---

## 7. Layer 4 — Unit Layer

### Purpose  
Reusable mid‑level logic.  
Units extend WebOS without touching System layer.

### Examples  
- Notification Unit  
- Analytics Unit  
- Wallet/Payment Unit  
- UserMeta Unit  

### Responsibilities  
- Shareable logic  
- Used across multiple apps  

### Rules  
- Units may depend on System  
- Units must NOT depend on any App  

---

## 8. Layer 5 — OS Layer

### Purpose  
OS tools inside WebOS.

### Responsibilities  
- System health checks  
- Task runners  
- Shell/CLI utilities  
- Internal monitoring  

### Examples  
- OS/SystemCheck  
- OS/TaskRunner  

### Rules  
- Only system‑maintenance logic  
- Zero UI or app logic  

---

## 9. Layer 6 — Event Layer

### Purpose  
Handle triggers and automations.

### Responsibilities  
- Cron jobs  
- Webhooks  
- Business events  
- Payment notifications  
- External system callbacks  

### Rules  
- Event → Unit/System → App  
- Never execute raw SQL in events  

---

## 10. Layer 7 — Database Layer

### Purpose  
Defines DB structure and management.

### Key Concepts  
- MDBMS (Multi‑Database Management System)  
- Table naming conventions  
- Soft delete (status 9)  
- Prefix‑based structure  

### Responsibilities  
- Maintain schema  
- Enable horizontal scaling  
- Avoid table conflicts  
- Support multiple ecosystems and tenants  

### Rules  
- CRUD only through Row / WebOS_Row  
- No raw SQL in apps  
- DB versioning must be consistent  

---

## 11. Layer 8 — App Layer

### Purpose  
The actual applications built on WebOS.

Examples:
- Wallet App  
- Exchange App  
- Admin Panel  
- ERP  
- CRM  
- Aeldo Shop  

### App Contains  
- App manifest  
- Modules  
- Templates  
- Components  
- CSS / JS  
- App‑level APIs  
- App‑level Events  

### Responsibilities  
- Present functionality to end-users  
- Combine System + Unit logic  
- Apply themes  
- Serve tenants  

### Rules  
- Must follow AliensStyle  
- Must remain DB-safe  
- Can never override System or Kernel  

---

## 12. Strict Layer Boundaries  
Each layer has strict allowed dependencies:

```
Boot → System → Kernel → Unit → OS → Event → App
```

Forbidden connections:
- App → Kernel (not allowed)  
- App → Boot (not allowed)  
- Kernel → App (not allowed)  
- System → App (not allowed)  

Allowed connections:
- App → System  
- Unit → System  
- OS → System  
- Event → Unit/System  

---

## 13. Full Execution Flow Sequence

### Web Request Flow  
```
User Request  
↓  
Boot Layer  
↓  
System Layer  
↓  
Kernel Layer  
↓  
Ecosystem Resolution  
↓  
App Resolution  
↓  
Module/Unit Execution  
↓  
Theme Rendering  
↓  
Final Output  
```

### Cron / Shell Flow  
```
Shell Entry  
↓  
Boot/shell.php  
↓  
System  
↓  
Kernel  
↓  
Event Handler  
↓  
Task Execution  
```

---

## 14. Multi‑Stack Layer Mapping  
WebOS (PHP) layers match:

### WebOS.JS  
- JS Loader  
- JS API  
- JS Auth  
- JS Cache  
- JS Hooks  
- JS Component  

### AppOS (Python)  
- Boot  
- System  
- Kernel  
- Modules  
- Runtime  

### DartOS (Flutter)  
- Boot  
- Engine  
- Kernel  
- Widget Units  
- Theme Layer  

This allows cross‑language architecture consistency.

---

## 15. AI Engineering Rules for Layers  
- Preserve architecture  
- Never merge System + App logic  
- Never skip Kernel  
- Use Units for reusable logic  
- Place UI only in App layer  
- Place engines only in System  

---

## 16. End of Document  
This file serves as the complete layer-by-layer reference for WebOS, used internally by the AlienCyborg SoftwareEngineer role.

