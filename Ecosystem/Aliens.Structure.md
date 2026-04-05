# Aliens.Structure (SSOT)

This document defines the **top-level structure of the `/Aliens` workspace** and how all major repositories relate.

This is a global ecosystem reference (not role-specific). Role-specific deep docs live under:
- `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/`

---

## 1) Canonical Roots (SSOT)

### Runtime code roots
- `/Aliens/WebOS/` — Operating system / engines / system layer
- `/Aliens/WebApp/` — Apps (theme-based UI surface)
- `/Aliens/WebBrand/` — Brand override layer (themes/layout/components)
- `/Aliens/WebSDK/` — Reusable modules/features/APIs
- `/Aliens/WebDB/` — Database repository (schemas/notes/tools)

### Documentation + orchestration
- `/Aliens/Docs/` — Public/project docs
- `/Aliens/Planning/` — Planning outputs
- `/Aliens/Cyborg/` — Cyborg knowledge + workflows (ACC)
- `/Aliens/.Alien/` — Developer runs/locks/planning artifacts
- `/Aliens/.Alien/{Developer_Username}/Memory/` — Conversation/task memory (scoped; never cross-app mix)
- `/Aliens/Project/{Developer_Username}/` — Planner/tasks CSV (SSOT root when workflow provides a CSV name)

---

## 2) Path Naming Notes
- WebApp root is **singular**: `/Aliens/WebApp/` (not `/Aliens/WebApps/`).
- WebOS Unit folder uses `Unit/` casing (SSOT inside workflow docs).

---

## 3) Relationship Map
- WebOS enforces: Boot → System → Kernel → Unit → OS → Event → App
- WebSDK provides reusable features and APIs.
- WebApp is the app UI surface (screens/sections/components/templates).
- WebBrand overrides UI shells and components across apps.
- WebDB provides DB conventions; Row engine is the DB gateway.

---

## 4) Versioning
Typical versioned roots:
- `/Aliens/WebOS/PHP.{Major}.{Minor}.{Patch}/...`
- `/Aliens/WebSDK/PHP.{Major}.{Minor}.{Patch}/...`
- `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/...`

---

## 5) SSOT References
- ACC paths: `Cyborg/AlienCyborg/Workflows/ACC/_Refs/ACC.Paths.md`
- ACC safety: `Cyborg/AlienCyborg/Workflows/ACC/_Refs/ACC.Safety.md`

---

# END
