# WebOS.MDBMS — Role.SoftwareEngineer Ecosystem  
(Detailed Version)

## 1. Introduction
MDBMS = **Multi‑Database Management System** inside WebOS.  
It is one of the **most powerful and advanced systems** ever built in Aliens WebOS, responsible for scaling databases for millions of users, multi‑ecosystem, multi‑tenant, multi‑app, and massive transactional workloads.

This document explains the complete internal architecture, rules, flow, routing logic, and AI‑safe engineering guidelines.

---

## 2. Why MDBMS Exists
Traditional single‑database systems break when:
- Data grows too large  
- Table rows cross millions  
- Write operations increase  
- Analytics load increases  
- Multiple apps share the same DB  
- Multiple ecosystems need isolation  
- Tenants need separation for security  

MDBMS solves this by:
- Splitting data intelligently  
- Routing queries to correct DB  
- Distributing load  
- Increasing performance  
- Keeping system stable at scale  
- Allowing real enterprise architecture  

---

## 3. MDBMS Design Philosophy
MDBMS follows five core design principles:

1. **Isolation**
   Each ecosystem/app/tenant can be isolated in its own database.

2. **Scalability**
   Heavy tables can be separated from light tables.

3. **Performance**
   Read/write operations are routed based on load type.

4. **Simplicity**
   Developer only calls:
   ```
   Row('table')->Get();
   ```
   MDBMS automatically decides everything else.

5. **AI‑Friendly Routing**
   AI should never manually open DB connections; MDBMS handles all.

---

## 4. MDBMS Types
WebOS MDBMS supports 7 split models:

### 4.1 Ecosystem-Based Splitting
Each ecosystem can have:
```
eco1_db
eco2_db
eco3_db
```

### 4.2 App-Based Splitting
Every app can have:
```
wallet_db
exchange_db
admin_db
erp_db
```

### 4.3 Tenant-Based Splitting
Each tenant (customer) can have:
```
tenant_1001_db
tenant_1002_db
```

### 4.4 Time-Based Splitting
For huge tables:
```
txn_2025_db
txn_2026_db
txn_2027_db
```

### 4.5 Table-Type Splitting
Light vs heavy tables:
```
core_db
logs_db
analytics_db
bigdata_db
```

### 4.6 Read/Write Splitting
WebOS MDBMS supports:
```
read_db
write_db
```

### 4.7 Shard-Based Splitting
Advanced (future version):
```
shard_01
shard_02
shard_03
```

---

## 5. MDBMS Routing Logic (Internal Flow)

### 5.1 Routing Steps
When developer calls:
```
Row('transaction')->Insert($Data);
```

MDBMS flow:

```
1. Identify table = "transaction"
2. Check ecosystem → eco1
3. Check app → exchange
4. Check tenant → t45
5. Check table type → heavy table
6. Check time partition → current month/year
7. Decide target database
8. Open correct DB connection via dbc()
9. Execute INSERT
10. Log result
11. Close connection via dbd()
```

The whole flow is automatic.

---

## 6. Table Classification Rules

### 6.1 Core Tables
- user  
- user_meta  
- ecosystem  
- app  
- module  

**DB:** core_db

### 6.2 Heavy Tables
- transaction  
- staking  
- orders  
- analytics  
- logs  

**DB:** heavy_db / bigdata_db

### 6.3 Tenant Tables
- tenant_settings  
- tenant_limits  
- tenant_branding  

**DB:** tenant-specific-db

### 6.4 App Tables
- wallet tables  
- exchange tables  
- admin tables  

**DB:** app-specific-db

---

## 7. File Responsibilities (Inside System Layer)

### 7.1 `System/Row.php`
- Primary CRUD engine  
- Calls MDBMS router  

### 7.2 `System/WebOS_Row.php`
- Adds ecosystem/app/tenant metadata  
- Extended Row engine  

### 7.3 `System/DB.php` or dbc() functions
- Handles connections  
- Opens/Closes DB  

### 7.4 `System/Config.php`
- Stores DB routing rules  
- Stores DB credentials  

---

## 8. Status & MDBMS Interaction
MDBMS **must always respect WebOS status model**:

| Status | Meaning |
|--------|---------|
| 1 | Active |
| 0 | Inactive |
| 9 | Soft Deleted |

Row Engine ensures:
- Deleted rows (status 9) are hidden  
- Insert/update operations maintain audit fields  
- DB routing still applies even when filtering by status  

---

## 9. Performance Enhancements

### 9.1 Index Strategy
AI should ensure:
- On heavy tables → composite indexes  
- On time-split DB → date-based index  
- On user tables → uid-based index  

### 9.2 Caching for MDBMS
Row Engine automatically:
- Caches schema mapping  
- Caches last-used DB connections  
- Caches table routing  

### 9.3 Connection Reuse
dbc() maintains:
- Short-lived persistent connections  
- Auto-reuse within request lifecycle  

---

## 10. MDBMS Safety Rules

### 10.1 Never Do:
- Direct mysqli_connect  
- Direct PDO connection  
- Raw SQL in apps  
- Hardcoded DB selection  
- Writing CRUD logic outside Row Engine  
- Using `DELETE FROM`  

### 10.2 Always Do:
- Use `Row()`  
- Use `WebOS_Row` for multi-ecosystem entities  
- Follow status model  
- Use `dbc() / dbd()` only inside system engines  

---

## 11. MDBMS Auto-Scaling Flow

```
If table grows > threshold:
    system automatically flags table as heavy
    → moves table to heavy_db
    → updates routing config
    → apps continue working without change
```

This supports:
- Non-stop scaling  
- Zero downtime migrations  
- Zero code updates required  

---

## 12. Multi-Ecosystem Example

### Ecosystem 1 → “Aliens”
```
aliens_core_db
aliens_analytics_db
```

### Ecosystem 2 → “Aeldo”
```
aeldo_core_db
aeldo_txn_db
```

Both use same codebase.  
MDBMS isolates data safely.

---

## 13. Multi-Tenant Example

Tenant 101 belongs to Aeldo ecosystem:
```
aeldo_t101_db
```

Tenant 102 uses shared DB:
```
aeldo_core_db
```

MDBMS decides routing automatically.

---

## 14. App-Based Example

Exchange App:
```
exchange_core_db
exchange_txn_db
exchange_order_db
```

Wallet App:
```
wallet_core_db
wallet_balance_db
```

Admin App:
```
admin_core_db
```

MDBMS ensures isolation while keeping digital integrations smooth.

---

## 15. AI Engineering Guidelines

AI must:
- Always use Row Engine  
- Never break MDBMS routing  
- Never remove routing rules  
- Always check table classification before writing CRUD logic  
- Maintain strict naming conventions  
- Ensure DB scaling is safe  

---

## 16. Future Enhancements (Planned)
- Automated sharding  
- Automated archive DB  
- Read/write load balancers  
- Multi-region DB replication  
- AI auto-index optimizer  

---

## 17. End of Document
This is the **complete detailed version** of WebOS MDBMS architecture for AlienCyborg SoftwareEngineer role.

