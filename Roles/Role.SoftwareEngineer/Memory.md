# AlienCyborg — Software Engineer Memory
# File: AlienCyborg/Roles/Role.SoftwareEngineer/Memory.md
# Scope: Role-specific engineering memory for SoftwareEngineer role

---

## 1. Purpose

This file stores **role-specific engineering memory** for the SoftwareEngineer role.
It captures patterns, decisions, and learnings that improve future performance.

---

## 2. Core Engineering Patterns

### 2.1 WebOS Patterns
- WebOS follows: Boot → System → Kernel → Unit → OS → Event → App
- App layer is thin — logic lives in Unit/System
- Row engine is the single gateway to database
- Multi-tenant: Ecosystem → App → Tenant resolution
- Soft delete uses status=9

### 2.2 AliensStyle Patterns
- PascalCase for everything
- $echo step-by-step markers in PHP
- Clean block structure
- Modular file organization
- Zero magic values

### 2.3 Module Patterns
- SDK modules follow template structure
- Each module has: API, Class, Component, Screen, SQL, JS
- Modules are reusable across apps
- Module naming follows WebOS conventions

---

## 3. Common Pitfalls (Avoid These)

- Never hardcode paths — use WebOS path resolution
- Never write raw SQL — use Row() engine
- Never modify Kernel files directly
- Never assume DB structure — check schema first
- Never skip AliensStyle compliance
- Never generate skeleton/placeholder code — always full implementation

---

## 4. Memory Scope

This memory applies ONLY to SoftwareEngineer role.
It does NOT apply to other roles.
It must NOT contradict Safety.md or System rules.

---

# END OF MEMORY
