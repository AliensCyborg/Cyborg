# DB.Tables.Ecosystem — Detailed Version
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Tables.Ecosystem.md`

This file explains **all ecosystem-level database tables**, their structure, purpose, relationships, and the exact way WebOS + Apps + SDK interact with them.  
This is one of the most critical documents because **Ecosystem Tables decide how 1000+ apps share a single WebOS installation.**

---

# 1. Purpose of Ecosystem Tables

Ecosystem tables:

- Identify which ecosystem (brand/platform) is active  
- Store configuration & metadata  
- Decide default apps, themes, modules  
- Enable **multi-ecosystem architecture** inside one Aliens installation  
- Provide configuration for all Apps + Modules + SDK engines  
- Ensure that WebOS can boot ANY ecosystem dynamically  

**Without ecosystem tables → WebOS cannot resolve anything.**

---

# 2. List of Core Ecosystem Tables

Inside your DB dump (base.software), the main ecosystem tables include:

1. `ecosystem`
2. `ecosystem_apps`
3. `ecosystem_theme`
4. `ecosystem_modules`
5. `ecosystem_settings`
6. `ecosystem_domains`
7. `ecosystem_git`
8. `ecosystem_meta`

We now describe each deeply.

---

# 3. Table: ecosystem

### Purpose:
This is the **master table** for defining each ecosystem (brand/business).

### Typical Columns:
```
id (INT, PK)
uid (UID string)
code (short identifier like ALIENS, AELDO, HX, etc.)
name (brand display name)
status (1 active, 0 inactive, 9 deleted)
created_at
updated_at
```

### Why important?
- First table WebOS reads during boot.
- Holds the identity of each ecosystem.
- Entire routing + app mapping depends on it.

---

# 4. Table: ecosystem_apps

### Purpose:
Defines which apps belong to which ecosystem.

### Key Columns:
```
id
ecosystem_id
app_code
is_default (1/0)
status
```

### Why important?
- Determines which app will load for a tenant or domain.
- Enables multi-app ecosystems:
  - Aliens → Social App, Wallet App, Admin App
  - E-Commerce Ecosystem → Store App, Seller App, Admin App

### AI Rule:
**Never assume only one app per ecosystem.**

---

# 5. Table: ecosystem_theme

### Purpose:
Assign a theme to an ecosystem or an app within that ecosystem.

### Columns:
```
id
ecosystem_id
theme_code
app_code (optional)
priority (override order)
status
```

### Why needed?
- Brand folder needs theme rules.
- Multi-brand/multi-app UI combinations become possible.

Example:
- Ecosystem A → Theme Light  
- Ecosystem B → Theme Dark  
- Ecosystem A + Wallet.App → Theme Blue  

---

# 6. Table: ecosystem_modules

### Purpose:
Defines which core or SDK modules are enabled for each ecosystem.

### Columns:
```
id
ecosystem_id
module_code
status
```

### Examples:
Ecosystem = “Aliens”
```
MLM.Tree → enabled
Wallet.Ledger → enabled
KYC.Basic → enabled
Notification.Suite → enabled
```

Ecosystem = “CompanyX”
```
MLM.Tree → disabled
Wallet.Ledger → enabled
```

---

# 7. Table: ecosystem_settings

### Purpose:
Stores custom settings for each ecosystem.

### Columns:
```
id
ecosystem_id
key
value
type (string/json/int/bool)
status
```

### Why critical?
- Determines business logic per ecosystem.
- Example:
  - Withdrawal fee
  - Trading fee
  - Default currency
  - Limits
  - Branding text

---

# 8. Table: ecosystem_domains

### Purpose:
Maps domains/subdomains to ecosystems.

### Columns:
```
id
ecosystem_id
domain
app_code (optional override)
status
```

### Why important?
- On every request, WebOS checks:
  - Which domain is this?
  - Which ecosystem does it belong to?
- Enables:
  - Multi-domain SaaS
  - Ecosystem-based routing

Example:
```
aliens.company → ALIENS ecosystem
shop.aeldo.com → AELDO ecosystem
hx.money → HXMONEY ecosystem
```

---

# 9. Table: ecosystem_git

### Purpose:
Stores Git branch/version info per ecosystem.

### Columns:
```
id
ecosystem_id
branch
version
last_synced_at
status
```

### Use cases:
- One ecosystem on PHP.25.11.00
- Another ecosystem on PHP.25.09.20
- Helps in CI/CD and rollout strategy

---

# 10. Table: ecosystem_meta

### Purpose:
Stores additional meta key-values.

### Columns:
```
id
ecosystem_id
meta_key
meta_value
status
```

### Used for:
- Caching meta decisions
- Boot-time advanced values
- Custom rules not covered by other tables

---

# 11. Relationships Between Ecosystem Tables

```
ecosystem (1) ----- (many) ecosystem_apps
ecosystem (1) ----- (many) ecosystem_theme
ecosystem (1) ----- (many) ecosystem_modules
ecosystem (1) ----- (many) ecosystem_settings
ecosystem (1) ----- (many) ecosystem_domains
ecosystem (1) ----- (many) ecosystem_meta
ecosystem (1) ----- (many) ecosystem_git
```

This creates a **complete definition** of an ecosystem.

---

# 12. Ecosystem Boot Flow in WebOS

During Boot:

1. Load domain  
2. Match domain → ecosystem_domains  
3. Fetch ecosystem record  
4. Load ecosystem_apps  
5. Determine active app  
6. Load ecosystem_theme  
7. Load ecosystem_modules  
8. Load ecosystem_settings  
9. Set global constant: `ecosystem`  
10. WebOS continues to System + Kernel

Thus ecosystem DB tables define:

- What to load  
- What to run  
- What to show  
- What modules are allowed  
- What business rules apply  

AI must ALWAYS respect these.

---

# 13. Multi-App + Multi-Ecosystem Example

### Case:
Domain = `wallet.aliens.com`

Flow:
- ecosystem_domains → ALIENS ecosystem
- ecosystem_apps → Wallet.App
- ecosystem_theme → AliensTheme.Default
- ecosystem_modules → Wallet.Ledger, Notification.Suite
- ecosystem_settings → Wallet fees

AI Responsibilities:
- Never load wrong app
- Never ignore settings
- Never bypass module enable/disable

---

# 14. MDBMS Interaction with Ecosystem Tables

When Row Engine selects DB:

- ecosystem_id defines which DB prefix to use
- tenant_id defines shard/partition
- Time period defines monthly/yearly DB

Example:
```
ecosystem ALIENS → DB1
ecosystem HX → DB2
ecosystem AELDO → DB3
```

---

# 15. Ecosystem-Level Security Rules

AI + developers MUST follow:

- Do not expose all apps to all ecosystems  
- Do not load modules not enabled for that ecosystem  
- Always check ecosystem_id during DB fetch  
- Always validate ecosystem_id in APIs  
- Always sanitize domain inputs  

---

# 16. Checklist

### When adding new ecosystem:
- [ ] Add row in ecosystem table  
- [ ] Add apps mapping  
- [ ] Add theme mapping  
- [ ] Add module mapping  
- [ ] Add domain mapping  
- [ ] Add settings (optional)  
- [ ] Test boot flow  

### When editing an ecosystem:
- [ ] Clear cache  
- [ ] Re-test domain routing  
- [ ] Update Git mapping  

---

# 17. Summary

`DB.Tables.Ecosystem.md` defines the core database tables that control **how WebOS decides everything**:

- Which ecosystem is active  
- Which apps load  
- Which modules run  
- How tenants behave  
- How DB selection happens  
- How the brand/theme/UI works  
- How multi-app, multi-brand SaaS becomes possible  

This is one of the MOST CRITICAL documents in the entire Cyborg/SoftwareEngineer system.

---

End of file  
`DB.Tables.Ecosystem.md`
