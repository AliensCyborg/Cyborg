# SQL Skill — Detailed Version (Role.SoftwareEngineer)

## 1. Overview
This file provides the complete SQL knowledge framework required by **AlienCyborg** in the **SoftwareEngineer** role.
It focuses on SQL inside the **Aliens WebOS MDBMS architecture**, where SQL is not just a query language but a **core engine** behind multi-tenant, multi-ecosystem, high-performance systems.

This document teaches Cyborg:
- How database architecture works inside WebOS
- When to write SQL and when NOT to write SQL
- How to use Row() / WebOS_Row instead of raw SQL
- How MDBMS partitions data across DBs
- Best practices for designing tables
- How to scale SQL for millions of records
- How to debug SQL the WebOS way
- How to create enterprise-grade relational structures

---

## 2. SQL Role Inside WebOS

SQL inside WebOS must follow these rules:

1. **SQL is not written everywhere.**
2. **All CRUD goes through Row() / WebOS_Row.**
3. **SQL must respect ecosystem + tenant logic.**
4. **Tables must be carefully designed for scale.**
5. **Table structure must use status-based soft delete.**
6. **SQL must avoid joins unless absolutely necessary.**
7. **SQL must avoid heavy GROUP BY / ORDER BY on big tables.**
8. **Indexes must be created strategically.**

SQL is treated as a **shared service inside the OS**, not an app-level tool.

---

## 3. MDBMS (Multi-Database Management System)

### 3.1 Purpose
WebOS uses MDBMS to:
- Fragment data across multiple databases
- Improve query speed
- Reduce table size
- Maintain isolation per tenant or ecosystem
- Support massive scale (millions of rows)

### 3.2 DB Selection Model
The Cyborg must know:
- Row() automatically selects the correct DB
- `dbc()` gives explicit DB selection (used rarely)
- `app`, `ecosystem`, and `tenant` may affect DB routing

### 3.3 Table Naming Convention
Consistent pattern:

```
{PREFIX}_{ENTITY}_{SUBENTITY}
```

Examples:
```
coinpods_user
coinpods_transaction
base_log
ecosystem_app
ecosystem_brand
```

### 3.4 Status-Based Lifecycle
Every table must include:

```
status TINYINT DEFAULT 1
```

Meaning:
- `1 = active`
- `0 = inactive`
- `9 = deleted`

WebOS does NOT delete rows physically.

---

## 4. WebOS SQL Access — Row Engine

Cyborg must ALWAYS use:

### 4.1 Row('table')->get()
Fetches a row by ID or UID.

### 4.2 Row('table')->insert($data)
Inserts records safely.

### 4.3 Row('table')->update($id, $data)
Updates using central rules.

### 4.4 Row('table')->status($id, 9)
Soft delete.

### 4.5 WebOS_Row
Adds:
- ecosystem enforcement
- tenant enforcement
- audit fields
- version control

---

## 5. Designing Tables (Cyborg Rules)

### 5.1 Always include:
```
id INT AUTO_INCREMENT
uid VARCHAR(50)
status TINYINT
created_at
updated_at
```

### 5.2 Use INT for relations, not UID
UID = for public
ID = for internal queries

### 5.3 Use indexing correctly:
- Index foreign keys
- Index UID
- Index status
- Index created_at (for big tables)

### 5.4 Avoid joins if table > 1M rows

Instead:
- Denormalize small fields
- Precompute analytics into metadata tables
- Use nightly batch jobs for heavy work

---

## 6. Large Table Strategy

For massive tables like `transactions`:

### 6.1 Partitioning Rules
Partition by:
- month
- ecosystem
- tenant

### 6.2 Summary Tables
Cyborg must maintain separate summary tables:
- daily totals
- weekly totals
- monthly totals
- yearly totals

### 6.3 Use CRON + incremental updates
Instead of computing huge aggregations live.

---

## 7. SQL Query Patterns (Allowed)

### 7.1 Simple SELECT
Allowed when using Row() wrapper.

### 7.2 Insert / Update
Must go via Row().

### 7.3 Limited SELECT for APIs
Use LIMIT + INDEX.

### 7.4 Where conditions
Ensure proper indexed columns.

---

## 8. SQL Query Patterns (Forbidden)

### No Joins on large tables
### No SELECT *
### No Unindexed columns
### No Heavy aggregation during app runtime
### No Raw SQL inside modules
### No Inline SQL with user input

These break multi-tenant scalability.

---

## 9. SQL Debugging (Cyborg Style)

Cyborg must:
- Use `$echo` logging for SQL operations
- Check slow logs
- Use EXPLAIN to inspect query plans
- Analyse index usage
- Detect table scans
- Reduce query complexity step-by-step

Debug Format Sample:

```
[SQL.DEBUG] Starting...
[SQL.DEBUG] Table = coinpods_transaction
[SQL.DEBUG] Filter = UID:1234
[SQL.DEBUG] Indexes used: transaction_uid_idx
[SQL.DEBUG] Duration = 0.0021s
```

---

## 10. SQL Inside API Design

Cyborg must:
- Never let API directly touch SQL
- APIs must use WebOS.Engine + Row()

API → Unit → System → Row → DB

Never API → DB directly.

---

## 11. Transaction Safety

WebOS must use:
- autocommit off for complex flows
- manual commit for multi-row operations
- fail-safe rollback system

Cyborg must know:
- Not every operation needs transaction
- Avoid long open transactions

---

## 12. SQL Security Rules

### 12.1 Always sanitize input
Row() already handles basic cleanup.

### 12.2 Do not expose SQL errors to user
Use internal logs.

### 12.3 Avoid `IN()` with thousands of items
Use temp mapping table instead.

### 12.4 Use prepared statements
WebOS Row already implements safe patterns.

---

## 13. SQL + PHP Integration Rules

Cyborg must:
- Avoid ORM heavy frameworks
- Use simple Row() instead
- Keep SQL logic minimal inside PHP
- Move heavy tasks to CRON jobs
- Use metadata tables for analytics
- Store large JSON in dedicated JSON fields if needed

---

## 14. Common SQL Entities in WebOS

### 14.1 Users Table
Indexes:
- uid
- mobile
- email
- status

### 14.2 Transactions Table
Indexes:
- uid
- type
- created_at
- status

### 14.3 App Table
Holds metadata about apps.

### 14.4 Ecosystem Table
Defines brand-level environments.

### 14.5 Git Table
Tracks version information.

---

## 15. Final Cyborg SQL Mindset

Cyborg must remember forever:

- SQL is a **tool**, not a battlefield.
- Row() is the gateway.
- All tables follow soft delete.
- Ecosystem + tenant context is mandatory.
- Big tables require special design.
- Queries must be precise, indexed, predictable.
- SQL must scale to millions of rows.
- Never break MDBMS logic.
- Always think 10 years ahead — BEM10Y mindset.

SQL inside Cyborg is not just SQL — it is part of the Operating System of Aliens Company.
