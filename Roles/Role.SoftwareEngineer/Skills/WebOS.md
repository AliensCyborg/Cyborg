# WebOS Skill — Detailed Version (Role.SoftwareEngineer)

## 1. Overview
This file defines the complete understanding, behaviour, and expertise required by the Cyborg (SoftwareEngineer role) to work with **Aliens WebOS**, the enterprise-grade multi-app, multi-tenant operating system powering all Aliens projects.

WebOS is treated here not as a framework, but as:
- an **Operating System for web applications**
- a **company-wide standard architecture**
- a **full-stack runtime with conventions**
- a **multi-layer execution model**
- a **DB-aware, tenant-aware, ecosystem-aware engine**

This document teaches the Cyborg:
- how WebOS thinks,
- how its layers interact,
- how to build features inside it,
- how to follow AliensStyle patterns,
- and how to avoid breaking the OS.

---

## 2. Core Responsibilities of a WebOS Engineer
A WebOS engineer inside Cyborg must be capable of:

1. Understanding Boot → System → Kernel → Unit → OS → Event → App lifecycle.
2. Selecting correct layers for correct features.
3. Always using `Row()` and `WebOS_Row` for DB operations.
4. Maintaining multi-tenant, multi-ecosystem logic.
5. Building new modules without breaking existing ones.
6. Following AliensStyle + WebOS rulebook.
7. Writing code that can scale to millions of users.
8. Building reusable components inside Units or SDK features.
9. Maintaining clear separation of:
   - Application logic
   - System logic
   - Kernel logic
10. Designing features so that AI agents can safely extend them.

---

## 3. WebOS Architecture (Cyborg-Level Understanding)

### 3.1 Boot Layer
- Entry point of all apps.
- Loads system, kernel, identity, ecosystem mapping.
- Prepares theme and module resolution.
- Should never contain business logic.

### 3.2 System Layer
This is the soul of WebOS.

Includes the engines:
- **Row Engine** – core CRUD
- **WebOS_Row** – high-level CRUD
- **Ecosystem Engine**
- **User Engine**
- **Theme Engine**
- **Module Engine**
- **Cache Engine**
- **UID Engine**
- **Git Engine**
- **App Engine**

Cyborg must know:
- System layer must remain stable.
- All new engines must follow the same naming and structure.
- Modify with extreme caution.

### 3.3 Kernel Layer
- OS-level security and validation.
- No business code allowed.
- Never modify unless explicitly instructed.

### 3.4 Unit Layer
- Reusable functional units.
- Logic for analytics, logs, notifications, etc.
- An ideal place for shared engines.

### 3.5 OS Layer
- System-level utilities.
- Backup, shell, admin tasks.

### 3.6 Event Layer
- Webhooks
- Cron events
- System events

### 3.7 App Layer
- Real-world application logic.
- Uses System engines + Units to build UI + API behaviour.

---

## 4. WebOS DB Philosophy — MDBMS

WebOS uses **Multi-Database Management System**:
- multiple DBs,
- multiple partitions,
- ecosystem-/tenant-aware routing,
- performance-optimized reads/writes.

The Cyborg must:
- NEVER directly call raw SQL
- ALWAYS use:
  - `Row()`
  - `WebOS_Row`
  - Multi-DB aware helpers

---

## 5. WebOS Module Structure

Each module has:
- `/template/`
- `/component/`
- `/css/`
- `/js/`
- `/api/`
- `/sql/`
- `/config.json`

Cyborg must:
- Always follow the structure.
- Always use AliensStyle naming.
- Never mix business logic into template files.
- Always bind APIs to modules using WebOS.Module engine.

---

## 6. WebOS Theme & Brand System

Theme system:
- Controls UI
- Controls layout
- Controls CSS/JS binding
- Loads theme from DB or manifest

Brand system:
- Overrides theme
- Sets brand-level variables
- Multi-theme support for multi-ecosystem

Cyborg must:
- Keep UI separate from logic
- Avoid hardcoded assets
- Respect theme hierarchy:
  Brand → Theme → App → Module → Component

---

## 7. WebOS Engines (Deep Knowledge)

Cyborg must master these engines:

### 7.1 WebOS.Loader
Handles dynamic loading of:
- modules
- components
- templates
- SDK features

### 7.2 WebOS.DB
Handles:
- AJAX communication
- API request structure

### 7.3 WebOS.Screen
Manages:
- page navigation
- animation
- dynamic partial loading

### 7.4 WebOS.Table
Provides table UI rendering engine.

### 7.5 WebOS.Cache
Provides advanced caching in front-end (JS side).

### 7.6 WebOS.API
Standard API call format for frontend.

### 7.7 WebOS.Auth
Authentication engine for frontend.

### 7.8 WebOS.Hooks
Event-injection system (future scope).

---

## 8. Coding Standards (AliensStyle + WebOS)

1. PascalCase everywhere
2. Clear echo-block debug sections
3. Strict file naming
4. No camelCase / snake_case
5. No inline SQL
6. No mixed responsibilities
7. Avoid complexity
8. Keep functions small, clean
9. Think like an OS engineer
10. Zero-error and zero-confusion mindset

---

## 9. How Cyborg Must Behave When Working With WebOS

- Always load system engines first.
- Never modify Kernel unless absolutely required.
- Build modules the WebOS way.
- Add features to SDK when they can be reused.
- Always think multi-tenant.
- Always think high-scale.
- Never break compatibility.
- Always follow AliensStyle.

---

## 10. Versioning Awareness

Cyborg must recognize:

- **PHP.25.09.xx** → initial multi-tenant engines
- **PHP.25.11.xx** → refined Boot/System/Kernel
- **PY.xx / JS.xx** → cross-platform WebOS

Cyborg must ensure:
- behaviour matches current WebOS version
- forwards compatibility mindset

---

## 11. Final Summary (Cyborg Mind Imprint)

Cyborg must permanently remember:

- WebOS is an Operating System.
- Boot → System → Kernel → Unit → OS → Event → App is sacred.
- Row() is the only way to touch the database.
- Multi-tenant logic must always be respected.
- Modules must stay clean and structured.
- System files are sacred.
- Apps are thin.
- System is fat.
- Kernel is untouchable.

This document should be hard-wired into the Cyborg's SoftwareEngineer role.
