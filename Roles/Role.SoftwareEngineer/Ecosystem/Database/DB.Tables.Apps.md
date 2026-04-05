# DB.Tables.Apps — Detailed Version  
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Tables.Apps.md`

This document explains **all App-related database tables** inside the Aliens Ecosystem:  
- What each table does  
- How WebOS uses it  
- How Apps interact with it  
- How the AI (AlienCyborg) must think about it  
- Rules for designing & modifying tables safely  

This is a core part of the **SoftwareEngineer → Ecosystem → Database Layer**.

---

# 1. Overview — How WebOS Handles Apps

WebOS supports **multi-app**, **multi-ecosystem**, **multi-tenant** architecture.

This means:

- One installation can run **100+ apps**
- Each ecosystem can pick which apps to enable
- Each tenant can enable/disable modules inside each app

All this configuration is stored in **App Tables**, collectively known as `app_*`.

---

# 2. Table: `app`

### Purpose:
The **master registry** of all apps.

### Structure:
```
id (PK)
ecosystem_id
code
name
default_theme
status
created_at
updated_at
```

### Meaning:
- `code` = unique app identifier (wallet / admin / exchange / userconsole)
- `ecosystem_id` connects app with its home ecosystem
- `default_theme` connects to theme system

### WebOS Usage:
- Boot → System/App.php loads this table
- App resolver uses `code` or `id` to activate the app
- Theme subsystem reads default theme

### AI Instructions:
- Never hardcode app IDs  
- Always fetch using `Row("app")`  
- Always filter `status = 1`  

---

# 3. Table: `app_meta`

### Purpose:
Store **metadata** for each app.

### Structure:
```
id
app_id
meta_key
meta_value
status
```

### Examples:
- `version = 1.4.2`
- `homepage = /dashboard`
- `permitted_roles = ["admin","manager"]`
- `dark_mode = true`

### Usage:
- Extend app configs without modifying code  
- Load dynamic properties  

---

# 4. Table: `app_route`

### Purpose:
Define which URL hits which controller/screen/API.

### Structure:
```
id
app_id
route
handler
type (api/view/internal)
method
status
```

### Examples:
```
/wallet/index → WalletIndex.php
/api/wallet/send → API_Wallet_Send.php
```

### WebOS Behavior:
- Router engine first checks this table
- App UI generation uses it too

---

# 5. Table: `app_menu`

### Purpose:
Store left-sidebar, topbar, or contextual menus.

### Structure:
```
id
app_id
title
icon
url
parent_id
sort
permission_key
status
```

### Behavior:
- WebOS loads menu → hides menus where user lacks permission  
- Hierarchical menu structure via `parent_id`  

### AI Rule:
Whenever generating UI flows → ALWAYS update or reference `app_menu`.

---

# 6. Table: `app_theme`

### Purpose:
Map which themes are available for an app.

### Structure:
```
id
app_id
theme_id
status
```

### Behavior:
- Apps can dynamically change theme  
- Multi-brand multi-app reused UI  

---

# 7. Table: `app_module`

### Purpose:
Defines which **modules** are active in an app.

### Structure:
```
id
app_id
module_code
status
config
```

Example modules:
```
wallet.transfer
wallet.history
exchange.order
exchange.kline
mlm.tree
```

### AI Constraint:
All module toggles must check this table, not code conditions.

---

# 8. Table: `app_access`

### Purpose:
Define **permissions** for each role.

### Structure:
```
id
app_id
role
module
action
status
```

Example action types:
- view  
- edit  
- delete  
- approve  

### Behavior:
- Permission Engine checks this table before rendering screen  
- App hides menu/route if permission denied  

---

# 9. Table: `app_manifest`

### Purpose:
Store App configurations like:
- APIs  
- Screens  
- Widgets  
- Layout settings  
- Dependencies  
- Required modules  

### Structure:
```
id
app_id
key
value
status
```

Example:
```
key = "routes"
value = JSON list of all routes
```

---

# 10. How All App Tables Work Together

```
           ecosystem
               |
               v
              app
      /        |              /         |          app_meta   app_route   app_menu
     |         |            |
     v         v            v
app_theme   app_module   app_access
                |
                v
          app_manifest
```

---

# 11. Multi-Ecosystem Rules

- Each ecosystem can have its own apps  
- Same app code can be reused across ecosystems  
- App appearance depends on theme mapping  
- App routing/menu/module behavior differs per-ecosystem  

---

# 12. AI Rules for Database Usage

### Always:
- Use `Row()` or `WebOS_Row()` for CRUD  
- Apply soft-delete rules  
- Validate input strictly  
- Respect `ecosystem_id` and `tenant_id`  

### Never:
- Use raw SQL  
- Hardcode IDs  
- Assume one ecosystem or tenant  
- Skip permission checks  

---

# 13. App Table Creation Guidelines

Before adding new tables:
- Ensure requirement is app-level, not SDK-level  
- Check if table already exists  
- Ensure multi-tenant support  
- Ensure multi-ecosystem support  

---

# 14. Summary

`DB.Tables.Apps.md` fully defines how app-related database tables work inside the Aliens ecosystem.

It ensures all developers + AI follow the same structure when:
- Creating apps  
- Adding routes  
- Adding menus  
- Adding themes  
- Building modules  
- Designing permissions  
- Designing manifests  

This file is **mandatory** for all future App, SDK, and WebOS engineering tasks.

---

End of File  
`DB.Tables.Apps.md`
