# DB.Overview — Detailed Version  
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Overview.md`

This document provides a complete, enterprise-grade, deeply structured overview of how databases operate across **Aliens WebOS → SDK → Apps → Ecosystems → Tenants**.  
It is written specifically for **AlienCyborg (Software Engineer Role)** so the AI can independently design, scale, optimize, debug, and evolve DB systems.

--- 

# 1. Purpose of WebOS Database System

The database layer exists to handle:

- Multi-ecosystem  
- Multi-app  
- Multi-tenant  
- High-performance workloads  
- Millions of rows per month  
- Time-based partitioning  
- Zero-downtime scaling  
- Automated backups  
- Smart routing  
- Predictable AI behavior  

The DB system must always remain:

**Fast, predictable, strongly structured, scalable, ecosystem-aware, tenant-aware, AI-friendly.**

---

# 2. DB Architecture Core Pillars

```
1. MDBMS (Multi-Database Management System)
2. Row Engine (Smart CRUD)
3. Table Structure Rules
4. Multi-Tenant System
5. High-Performance Query Rules
6. Backup / Restore / Sharding
```

These pillars ensure consistency across all apps/modules.

---

# 3. MDBMS — Multi-Database Management System

MDBMS solves:

- Large table performance issues  
- Monthly/yearly partitioning  
- Ecosystem-wise DB separation  
- Tenant-wise DB isolation  
- Time-based DB archiving  
- Sharding and horizontal scaling  

### MDBMS Core Patterns:
- DB by ecosystem  
- DB by tenant  
- DB by module group  
- DB by month (for heavy logs)  
- DB by year (for yearly archival)  

### Example Routing:
```
Wallet transactions → db_wallet_{YYYY_MM}
Exchange orders     → db_exchange_core
Logs                → db_logs_{YYYY_MM}
Analytics           → db_analytics
```

Row Engine automatically decides the DB based on logic.

---

# 4. Row Engine — Smart CRUD Brain

Row Engine is the **only correct way** to touch the database.

It handles:

- DB selection  
- Table selection  
- Tenant filtering  
- Ecosystem filtering  
- Soft-delete  
- Prefix rules  
- Logging  
- Debugging  
- Insert/update sanitization  
- Partition detection  
- Status-based lifecycle (1,0,9)  

### NEVER allowed:
- Raw SQL inside APIs  
- Raw SQL inside App  
- Direct DB handle usage  

### ALWAYS:
- Row("table")  
- WebOS_Row for advanced metadata  

---

# 5. Table Naming Rules

### Structure:
```
{ecosystemCode}_{appCode}_{entity}
```

Example:
```
ali_wallet_transaction
ali_exchange_order
```

### Required Columns:
```
id (PK)
uid (Universal ID)
ecosystem_id
tenant_id
status
created_at
updated_at
```

### Optional But Recommended:
```
created_by
updated_by
ip
metadata (JSON)
```

### Benefits:
- Uniformity  
- Partition-friendly  
- AI-readable  
- Ecosystem-safe  

---

# 6. Tenant & Ecosystem Metadata

Every row MUST have:

- ecosystem_id  
- tenant_id  

This ensures:

- strict isolation  
- security  
- correct Row Engine behavior  
- analytics correctness  

---

# 7. High-Performance Rules

### 7.1 Never use SELECT *
### 7.2 Max 3 table joins
### 7.3 Partition tables above 1M rows
### 7.4 Mandatory Indexes:
```
uid
ecosystem_id
tenant_id
status
created_at
```

### 7.5 Row Engine auto-filters:
- tenant safety  
- ecosystem safety  
- status != 9  

---

# 8. Backup System Rules

Backups MUST support:

- Full backup  
- Incremental backup  
- Time-based backup  
- Multi-db backup  
- Auto-rotation  
- DB versioning  

Backup paths should be:

```
/backup/{db}/{YYYY}/{MM}/{db-version}.sql
```

---

# 9. Restore & Migration Rules

### Migration Rules:
- Column addition only at end, avoid breaking changes  
- Every migration documented  
- Index updates allowed  
- Partition changes allowed  

### Restore Rules:
- Prefix preserved  
- Tenant IDs preserved  
- Ecosystem mappings intact  

---

# 10. Multi-DB Routing (Detailed)

Routing flow:

```
1. Identify ecosystem
2. Identify tenant
3. Identify module
4. Identify table
5. Identify date partition
6. Select correct DB
```

Row Engine uses internal logic to ensure correctness.

---

# 11. Partitioning Logic

### Use partitioning for:
- Transactions  
- Orders  
- Logs  
- Wallet events  
- Analytics tables  

Partition types:

```
Monthly partitions
Yearly partitions
Module-based DB groups
Tenant-specific DBs
```

---

# 12. DB Health & Monitoring

Daily checks:

- Table count  
- DB size  
- Slow queries  
- Index fragmentation  
- Log volume  
- Connection load  

Advanced future checks:

- AI-based query anomaly detection  
- Auto-index suggestions  
- Auto-partition trigger  

---

# 13. AI Rules for DB Work (AlienCyborg Role)

### ALWAYS:
- Use Row Engine  
- Respect ecosystem/tenant  
- Suggest partitioning  
- Maintain performance  
- Avoid raw SQL  
- Ensure backward compatibility  

### NEVER:
- Write direct queries  
- Break Row Engine rules  
- Hardcode DB names  

---

# 14. Developer Rules

### MUST:
- Follow naming patterns  
- Use Row Engine 100%  
- Maintain multi-tenant logic  
- Document DB migrations  
- Write clean, index-friendly tables  

### MUST NOT:
- Write business logic in App APIs  
- Access DB directly  
- Leak table names in responses  

---

# 15. Summary

`DB.Overview.md` defines the **entire database philosophy** of Aliens WebOS:

- MDBMS  
- Row Engine  
- Table rules  
- Performance rules  
- Backup/restore rules  
- Partitioning logic  
- AI + developer guidelines  

This ensures the system remains:

**Ultra-fast, scalable, safe, consistent, predictable, and future-proof.**

---

End of File  
`DB.Overview.md`
