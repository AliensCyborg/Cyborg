# WebOS.Structure — Software Engineer Role (SSOT)

This document is the **structure-level entrypoint** for WebOS, for the Software Engineer role.

If you need the full conceptual overview, read:
- `Roles/Role.SoftwareEngineer/Ecosystem/WebOS/WebOS.Overview.md`

---

## 1) Canonical WebOS Root
- `/Aliens/WebOS/`

WebOS is versioned by folder:
- `/Aliens/WebOS/PHP.{Major}.{Minor}.{Patch}/...`

Example:
- `/Aliens/WebOS/PHP.26.01.00/`

---

## 2) WebOS Layering (Must Respect)
WebOS must be treated as layered architecture:
- Boot → System → Kernel → Unit → OS → Event → App

Rules:
- Kernel is protected: do not bypass.
- Unit layer is reusable building blocks.
- System layer is orchestration + engines.

---

## 3) Engines (Language-specific)

### 3.1 PHP Engines
Common pattern:
- `/Aliens/WebOS/PHP.{Version}/Engine/WebOS.{EngineName}.php`

### 3.2 Unit Engines (SSOT casing)
Unit engines are stored under:
- `/Aliens/WebOS/PHP.{Version}/Unit/js/...`
- `/Aliens/WebOS/PHP.{Version}/Unit/bash/...`
- `/Aliens/WebOS/PHP.{Version}/Unit/sql/...`

Important:
- Use `Unit/` casing consistently in docs and workflows.

---

## 4) Safety Boundaries
Do not refactor/edit core WebOS layers without explicit instruction:
- Boot
- System
- Kernel
- Row engine
- UID logic

---

## 5) Common Engineering Tasks
- Add/Update Engine (PHP/JS/Bash/SQL)
- Fix path conventions (SSOT first)
- Debug engine behavior using echo tracing + reproducible inputs

---

## 6) Cross-References (Role SSOT)
- WebOS overview: `Roles/Role.SoftwareEngineer/Ecosystem/WebOS/WebOS.Overview.md`
- WebOS security: `Roles/Role.SoftwareEngineer/Ecosystem/WebOS/WebOS.Security.md`
- WebOS layers: `Roles/Role.SoftwareEngineer/Ecosystem/WebOS/WebOS.Layers.md`

---

# END
