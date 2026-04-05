# WebOS.Engines (SSOT)

This document is a global summary of **WebOS engine types**, locations, and safe creation/update rules.

Detailed conceptual overview:
- `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/WebOS/WebOS.Overview.md`

---

## 1) Engine Categories

### 1.1 PHP Engines (Primary)
Canonical pattern:
- `/Aliens/WebOS/PHP.{Version}/Engine/WebOS.{EngineName}.php`

Rules:
- Keep engines small and single-purpose.
- Prefer WebOS System engines over app-local duplication.

### 1.2 Unit Engines (Shared building blocks)
Canonical pattern:
- `/Aliens/WebOS/PHP.{Version}/Unit/js/{EngineBaseName}.js`
- `/Aliens/WebOS/PHP.{Version}/Unit/bash/{EngineBaseName}.sh`
- `/Aliens/WebOS/PHP.{Version}/Unit/sql/{EngineBaseName}.sql`

---

## 2) Naming
- Use `WebOS.*` prefix for system engines.
- Use explicit suffix when helpful (`*Cache`, `*Row`, `*Theme`, `*Auth`).

---

## 3) Safety Rules
- Never bypass Kernel.
- DB operations must use Row engine.
- Do not introduce incompatible behavior across versions.

---

## 4) Create vs Update Discipline
- Create workflows: allowed to create new engine file at valid SSOT path.
- Update workflows: must verify target exists, then patch.

---

## 5) Workflow References
Engine workflows live under:
- `Cyborg/AlienCyborg/Workflows/ACC/WebOS/`

---

# END
