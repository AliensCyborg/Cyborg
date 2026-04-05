# DB.MDBMS.Routing — Detailed Version
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.MDBMS.Routing.md`

This document explains **how WebOS MDBMS (Multi-Database Management System) performs intelligent routing**, choosing the correct database at runtime based on ecosystem, tenant, module, table type, date range, sharding rule, performance level, and archival layer.

This is one of the **core engines** of the entire Aliens Ecosystem and critical for high‑scale systems (10M+ users, billions of rows, multi‑tenant + multi‑ecosystem setups).

---

# 1. MDBMS Routing — Core Philosophy

MDBMS = System that automatically decides **which database to read/write from**, based on:

```
Ecosystem
Tenant
App
Module
Table Type
Data Size
Date / Time Slice
Performance Tier
Archival Policy
```

**Goal:**  
Zero manual DB selection.  
Everything automatic, safe, predictable, high‑performance.

---

# 2. Why MDBMS Exists

Because Aliens Ecosystem supports:

- Multiple ecosystems  
- Multiple tenants  
- Multiple apps  
- Multiple modules  
- Multi‑brand & multi‑deployment  
- Huge tables (transactions, logs, analytics)  
- Fast growth  

This makes a **single database** impossible to scale.

MDBMS solves:

1. Load balancing  
2. Read/write isolation  
3. Archive management  
4. Auto‑sharding large tables  
5. Distributed data across nodes  
6. Zero-downtime scaling  
7. Per‑tenant isolation  

---

# 3. MDBMS Routing Modes

MDBMS supports **six routing modes**:

### 3.1 Lifecycle Routing (Most Important)
Tables are categorized:

| Table Type | Examples | Routing |
|-----------|----------|---------|
| **Master** | users, tenants, ecosystems | Always `DB_MAIN` |
| **Operational** | orders, wallet, transactions | MDBMS auto routes based on load |
| **Analytical** | daily logs, reports, aggregates | Usually `DB_ANALYTICS` |
| **Archive** | old orders, old transactions | `DB_ARCHIVE` |
| **Logs** | events, error logs, API logs | `DB_LOGS` |
| **Cache DB** | caching, temp tables | Redis / memory DB |

---

### 3.2 Time-Based Routing
Heavy tables are split by:

- Month  
- Quarter  
- Year  
- Custom time slice  

Example tables:

```
transactions_2025_01
transactions_2025_02
transactions_2025_03
```

Routing rule:

```
If created_at ∈ Jan 2025 → transactions_2025_01
```

---

### 3.3 Ecosystem-Based Routing

Each ecosystem can have its own DB:

```
Aliens → aliens_main
Aeldo  → aeldo_main
Coinpods → coinpods_main
```

Routing rule:

```
Row("wallet") → database_of(ecosystem)
```

---

### 3.4 Tenant-Based Routing (SaaS Mode)

Each tenant can have:

- Shared database  
- Dedicated database  
- Dedicated shards  

Routing rule:

```
If tenant.db_mode = dedicated:
    SELECT tenant_specific_db
Else:
    SELECT shard_for_multi_tenant_group
```

---

### 3.5 Module-Based Routing

Each MODULE can define:

- preferred DB  
- storage layer  
- archival policy  

Example:

```
MLM.Tree → DB_HIGH_PERFORMANCE
OrderMatching → DB_REALTIME
Analytics → DB_ANALYTICS
Logs → DB_LOGS
```

---

### 3.6 Performance Tier Routing

WebOS supports performance tiers:

| Tier | Purpose |
|------|---------|
| **Tier 0** | Realtime mission-critical (OrderMatching) |
| **Tier 1** | High performance ops (Wallet, KYC) |
| **Tier 2** | Normal operations (User, Settings) |
| **Tier 3** | Logs & Archive |

Routing decides correct DB cluster based on tier.

---

# 4. MDBMS Routing Engine — How it Works

### 4.1 Core Routing Formula

```
db = resolve(
    ecosystem,
    tenant,
    module,
    table,
    table_type,
    time_slice,
    performance_tier
)
```

### 4.2 Resolution List (Priority Order)

1. Explicit tenant DB  
2. Explicit module DB  
3. Ecosystem DB  
4. Table type DB  
5. Time slice DB  
6. Default DB  

### 4.3 Example

Transaction table:

```
ecosystem: Coinpods
tenant: 14
table: transactions
module: Wallet
performance: Tier1
time: 2025-02-05
```

Routing evaluation:

1. No direct tenant DB  
2. Wallet module requires performance DB  
3. Ecosystem = Coinpods → base DB  
4. Table type = operational  
5. Time slice → transactions_2025_02  
6. Final DB selected → coinpods_ops_2025_02  

**This happens automatically via Row() engine.**

---

# 5. Row() Engine Integration

### Row() calls MDBMS using:

```
Row("transactions")->insert(data)
Row("orders")->get(id)
Row("wallet")->update(id, fields)
```

Row() internally calls:

```
MDBMS.Resolve(table, args...)
```

Then runs the query on the selected DB.

This keeps **all App/SDK/WebOS code DB-agnostic**.

---

# 6. Table Classification Engine

### Table types must be pre‑declared in:

```
/Database/Table.Meta.json
```

or inside:

```
module.json
```

Example Classification:

```
users → master
wallet → operational
transactions → operational
transactions_2024_12 → archive
failed_logs → logs
api_logs → logs
analytics_daily → analytical
```

This classification decides the routing path.

---

# 7. Sharding Modes

### 7.1 Horizontal Sharding
Based on:

- UID hash  
- Tenant ID  
- Ecosystem ID  
- Time  

Example:

```
Shard = UID % 4 → choose db_wallet_shard_0 to 3
```

### 7.2 Vertical Sharding
Split per module:

```
wallet_* → wallet_db
orders_* → order_db
mlm_* → mlm_db
analytics_* → analytics_db
```

---

# 8. Architecture Diagram (Text-Based)

```
                      ┌─────────────────────────────┐
                      │        WebOS System         │
                      └───────────────┬─────────────┘
                                      │
                               Row() Engine
                                      │
                          MDBMS Routing Engine
                                      │
          ┌───────────────────────────┼──────────────────────────┐
          │                           │                          │
   Ecosystem Resolver        Tenant Resolver            Time Slice Resolver
          │                           │                          │
          └──────────┬────────────────┴──────────────┬───────────┘
                     │                               │
             Module-Based DB                  Table-Type DB
                     │                               │
                     └──────────┬────────────────────┘
                                │
                         Final DB Selected
```

---

# 9. AI Rules for MDBMS

### AI MUST ALWAYS:

- Use Row() — NEVER write raw SQL  
- Respect table types  
- Respect tenant routing  
- Respect ecosystem routing  
- Respect module DB preferences  
- Do archival routing based on date  
- ALWAYS log routing path  
- Detect incorrect routing during code generation  

### AI MUST NEVER:

- Hardcode database names  
- Hardcode table suffixes  
- Skip MDBMS rules  
- Assume one DB fits all  

---

# 10. Developer Checklist

Before writing any DB-heavy feature:

- [ ] Did you check table type?  
- [ ] Did you define module DB preference?  
- [ ] Are you using Row() only?  
- [ ] Does your logic require sharding or time slicing?  
- [ ] Did you ensure backward compatibility?  
- [ ] Did you check AI’s routing interpretation?  

---

# 11. Summary

This document formalizes **MDBMS Routing**, ensuring:

- Enterprise-grade scalability  
- Multi-ecosystem support  
- Tenant isolation  
- Zero-downtime expansion  
- AI-driven safe DB access  
- Predictable performance under massive load  

This is one of the most important files for WebOS & SDK features.

---

End of File  
`DB.MDBMS.Routing.md`
