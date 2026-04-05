# App.Structure — Software Engineer Role (SSOT)

This document is a **structure-level entrypoint** for how Apps are represented and mounted in the Aliens ecosystem.

Detailed reference:
- `Roles/Role.SoftwareEngineer/Ecosystem/Apps/Apps.Structure.md`

---

## 1) Two App Views (Important)

### 1.1 Runtime App Surface (WebApp)
In this workspace, the canonical runtime UI surface is:
- `/Aliens/WebApp/{AppName}/{Version}/theme/{AppTheme}/User/...`

This is the structure used by ACC WebApp workflows (components, screens, sections, templates, layouts).

### 1.2 Conceptual App Package (Architectural)
The architectural “App container” (modules, API, hooks, assets) is defined in:
- `Roles/Role.SoftwareEngineer/Ecosystem/Apps/Apps.Structure.md`

Rule:
- Use the **existing SSOT** for the current system you are editing.

---

## 2) WebApp Theme Structure (Canonical)
- Layout: `User/layout/`
- Screen: `User/screen/`
- Section: `User/section/`
- Component: `User/component/`
- CSS: `User/css/`
- Template: `User/template/{TemplateName}/...`

---

## 3) Core App Rules
- No duplicate business logic in UI files.
- Use WebOS/System engines for orchestration.
- Use WebSDK modules for reusable features.
- Respect WebBrand overrides.

---

## 4) Cross-References
- Apps blueprint: `Roles/Role.SoftwareEngineer/Ecosystem/Apps/Apps.Structure.md`
- ACC paths SSOT: `Workflows/ACC/_Refs/ACC.Paths.md`
- Brand structure: `Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.Structure.md`

---

# END
