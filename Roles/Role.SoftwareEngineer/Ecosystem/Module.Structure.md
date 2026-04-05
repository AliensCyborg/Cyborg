# Module.Structure — Software Engineer Role (SSOT)

This document defines what a **Module** means in the Aliens ecosystem and where it lives.

Primary reference (SDK/module system):
- `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.ModuleSystem.md`

---

## 1) Module = Reusable Feature Unit
A module is a unit of functionality that can be:
- called by apps
- called by WebOS engines
- exposed as API
- scheduled via cron
- triggered via webhooks

---

## 2) Canonical Module Root
- `/Aliens/WebSDK/PHP.{Version}/Module/{ModuleName}/`

Typical layout:
- `php/index.php` (module entry/class)
- `api/*.php`
- `cron/*.php`
- `webhook/*.php`

---

## 3) Module Contracts
A good module must define:
- input contract
- output contract
- permissions/identity rules
- DB tables it touches (via Row engine)

---

## 4) Module Naming
- Use PascalCase for module folder names if that is the existing convention.
- Keep API names explicit: `{Module}{Action}.php`.

---

## 5) Cross-References
- SDK structure: `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.Structure.md`
- API rules: `Roles/Role.SoftwareEngineer/Ecosystem/SDK/SDK.API.Rules.md`
- Apps/modules mapping: `Roles/Role.SoftwareEngineer/Ecosystem/Apps/Apps.Modules.md`

---

# END
