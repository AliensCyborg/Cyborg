# AliensSDK.Structure — Software Engineer Role (SSOT)

This document defines the canonical **Aliens SDK (WebSDK) structure** that engineers + AI must follow.

Primary detailed reference:
- `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.Structure.md`

---

## 1) Canonical Root
- `/Aliens/WebSDK/`

Versioning pattern:
- `/Aliens/WebSDK/PHP.{Major}.{Minor}.{Patch}/...`

---

## 2) Module System (High-Level)
WebSDK is organized by modules:
- `/Aliens/WebSDK/PHP.{Version}/Module/{ModuleName}/`

Common subfolders (module-local):
- `php/` (module entry / class)
- `api/` (HTTP endpoints)
- `cron/` (scheduled jobs)
- `webhook/` (external triggers)
- `docs/` (module docs)

---

## 3) Core Rules
- SDK holds reusable features and contracts.
- Apps/WebOS should call SDK features instead of duplicating logic.
- Naming should be predictable and AI-discoverable.

---

## 4) Relationship Map
- WebOS: system + engines + enforcement layer
- WebSDK: reusable feature warehouse
- WebApp: app UI + theme surface
- WebBrand: override UI layer

---

## 5) Cross-References
- Detailed structure: `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.Structure.md`
- Module system: `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.ModuleSystem.md`
- API rules: `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.API.Rules.md`

---

# END
