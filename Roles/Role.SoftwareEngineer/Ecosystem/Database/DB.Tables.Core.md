# DB.Tables.Core — Detailed Version
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Tables.Core.md`

This file documents the **core tables** required across the entire Aliens WebOS + SDK + Apps ecosystem.  
These are **fundamental database entities** that MUST exist in every ecosystem, because WebOS depends on them to:

- Boot correctly  
- Resolve apps  
- Resolve tenants  
- Resolve themes  
- Resolve modules  
- Resolve users  
- Resolve settings  
- Resolve ecosystem  
- Resolve routing  
- Resolve versioning  

Without these tables, *no WebOS system can run*.

---

# 1. Purpose of Core Tables

Core tables provide the **infrastructure** for:

- Multi-ecosystem system  
- Multi-app architecture  
- Multi-tenant support  
- Feature toggles  
- User identity  
- Routing  
- Brand/theme selection  
- Modules  
- Database routing (MDBMS)  
- Git version tracking  
- App manifest linking  

These tables MUST stay consistent across all installations.

---

# 2. List of Core Tables

Below are **mandatory core tables** inside the WebOS database layer:

```
ecosystem
app
tenant
theme
module
module_units
app_theme
app_module
ecosystem_app
ecosystem_theme
ecosystem_module
ecosystem_settings
tenant_settings
app_settings
users
user_roles
user_meta
git
library
cdns
logs
```

We divide them into **7 groups**:

```
Group A — Ecosystem Core
Group B — App Core
Group C — Tenant Core
Group D — User Core
Group E — Module Core
Group F — Theme/Brand Core
Group G — System / Utility Core
```

---

# 3. Group A — Ecosystem Core Tables

These tables define the top-level container called *Ecosystem*.

### 3.1 `ecosystem`
Purpose:
- Identify each ecosystem (Aliens, Aeldo, Coinpods, HX Money, etc.)
- Define ecosystem-level configuration

Important fields:
```
id
code
name
description
status
created_at
updated_at
```

### 3.2 `ecosystem_settings`
Stores:
- Branding defaults
- Routing defaults
- App defaults
- CDN defaults
- Default theme
- Default modules

### 3.3 `ecosystem_app`
Maps:
- Which app belongs to which ecosystem
- Enabled/disabled status
- Version tracking

### 3.4 `ecosystem_theme`
Maps:
- Which themes are available in the ecosystem

### 3.5 `ecosystem_module`
Maps:
- Which modules/features an ecosystem supports

---

# 4. Group B — App Core Tables

### 4.1 `app`
This is the **most important** table after ecosystem.

WebOS reads this table during boot:

- Which apps exist  
- Which app is default  
- Current version  
- Current theme  
- App manifest  
- App routing  

Fields:
```
id
code
name
slug
version
status
manifest
created_at
updated_at
```

### 4.2 `app_settings`
Stores:
- App-level toggles
- App-level config
- Menu visibility
- Limits

### 4.3 `app_module`
Maps:
- Modules required by app
- SDK modules linked to app
- App-specific modules

### 4.4 `app_theme`
Maps:
- Theme per app  
- Override system activation  
- Adaptive brand support

---

# 5. Group C — Tenant Core Tables

Tenants are second-level containers inside apps.

### 5.1 `tenant`
Holds:
- Tenant-level info
- Branding (logo, colors)
- Feature visibility
- App selection

Key fields:
```
id
ecosystem_id
app_id
code
name
settings
status
```

### 5.2 `tenant_settings`
Stores:
- Feature toggles
- Pricing plan toggles
- App module enable flags
- Theme overrides

This enables:
- One app → different tenant experiences
- One ecosystem → thousands of tenants

---

# 6. Group D — User Core Tables

### 6.1 `users`
The fundamental identity table.

Fields:
```
id
uid
ecosystem_id
tenant_id
role_id
name
email
phone
password
status
created_at
updated_at
```

### 6.2 `user_roles`
Defines system roles such as:
- SuperAdmin
- EcosystemAdmin
- TenantAdmin
- Staff
- User

### 6.3 `user_meta`
Stores:
- Additional attributes  
- Preferences  
- KYC data  
- Points, balance, etc.

User_meta allows infinite extension.

---

# 7. Group E — Module Core Tables

Modules define reusable feature blocks.

### 7.1 `module`
Contains:
```
id
code
name
category
sdk_dependency
status
```

### 7.2 `module_units`
Defines:
- Sub-features
- Actions
- Screens
- Permissions

Example:
```
module: wallet
unit: deposit
unit: withdraw
unit: statements
```

---

# 8. Group F — Theme & Brand Core Tables

### 8.1 `theme`
Defines:
- Brand theme
- Font
- Base color
- CSS rules
- Layout rules

### 8.2 `app_theme`
Platform entry combining:
- App
- Theme
- Overrides

Used during rendering.

---

# 9. Group G — System / Utility Core

### 9.1 `git`
Stores:
- Git branch  
- Commit  
- Version  
Used by:
- WebOS version display  
- System audit  
- Deployment tools  

### 9.2 `library`
Stores third-party dependencies information.

### 9.3 `cdns`
Maps all CDNs used by system.

### 9.4 `logs`
Used by:
- WebOS logging engine  
- API logs  
- Error logs  
- Access logs  

---

# 10. AI (AlienCyborg) Rules for Working With Core Tables

### Must Always:
- Use Row engine  
- Never change structure without migration  
- Respect multi-ecosystem model  
- Respect multi-tenant model  
- Respect soft-delete model  
- Use status field properly  

### Must Never:
- Write raw SQL  
- Hard-delete rows  
- Modify core tables without System review  

---

# 11. Core Table Rules

### 11.1 All Core Tables Must Contain:
- `id` (int)
- `status` (int)
- `created_at`
- `updated_at`

### 11.2 UID fields NEVER stored in integer form for users
Always use string UID.

### 11.3 Multi-ecosystem compatibility is mandatory

### 11.4 Linking tables must use:
```
ecosystem_id
tenant_id
app_id
```

### 11.5 Naming rules:
Lowercase, underscores allowed.

---

# 12. Summary

`DB.Tables.Core.md` defines:

- All required tables  
- Their purpose  
- Their responsibility  
- Their relationship  
- AI behavior rules  
- WebOS dependency rules  

These tables act as the **foundation** of the entire Aliens ecosystem.

If any table is removed or missing:
- WebOS will fail to boot  
- Apps will fail to load  
- Tenants will break  
- Themes won’t work  
- API routing will fail  

Therefore this document MUST always stay complete, accurate, and updated.

---

End of File  
`DB.Tables.Core.md`