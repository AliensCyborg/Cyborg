# WebOS.RowEngine — Role.SoftwareEngineer Ecosystem  
(Detailed Version)

## 1. Introduction
The **Row Engine** is the heart of WebOS database operations.  
It is the single most important system for CRUD, logging, MDBMS routing, security, and consistency.

This document gives a **deep internal breakdown** of how the Row Engine works, how it decides which DB to use, how it handles inserts/updates, and how AI + Human developers must interact with it.

---

## 2. Purpose of the Row Engine
The Row Engine was created to solve the biggest problems in enterprise systems:

- Direct SQL everywhere → unpredictable behaviour  
- Multi-database scaling issues  
- Table naming inconsistencies  
- Missing soft-delete discipline  
- Manual logging → difficult debugging  
- High developer error rate  
- Query duplication  
- Security vulnerabilities  

The Row Engine provides:
- A central and controlled access gateway  
- Uniform CRUD operations  
- Automatic MDBMS selection  
- Audit logging  
- Soft-delete rules  
- Consistent naming behaviour  
- High safety even with junior developers  
- AI-friendly, pattern-safe behaviour  

---

## 3. Core Philosophy
```
Never interact with the database directly.
Always use the Row / WebOS_Row Engine.
```

This ensures:
- Safe DB access
- Cleaner code
- Predictable data flow
- Easy debugging
- Cross-app compatibility
- Zero conflicts in shared databases
- Easy migration across ecosystems

---

## 4. High-Level Responsibilities

### 4.1 CRUD Operations
The Row Engine must support:
- row_get(id)
- row_filter(criteria)
- row_insert(data)
- row_update(id, data)
- row_status(id, status)

### 4.2 DB Routing via MDBMS
Based on:
- Ecosystem  
- Table type  
- Time partition (if enabled)  
- Tenant  
- App  

### 4.3 Logging & Debugging
Every Row operation logs:
- SQL query
- Bind values
- Execution time
- Row count
- Errors

### 4.4 Automatic Soft Delete
Instead of physical deletion:
```
status = 9  (deleted)
```

### 4.5 Error Isolation
- Capture bad queries  
- Prevent fatal failures  
- Provide debug-friendly output  

---

## 5. Internal Flow of Row Engine

### 5.1 Step-by-step Execution
Example for a SELECT:

```
Input → Row(table)->get(id)
↓
Validate table name
↓
Apply prefix rules
↓
Resolve DB using MDBMS rules
↓
Prepare SQL
↓
Execute SQL
↓
Apply status rules (exclude deleted rows)
↓
Return single row
```

Example for INSERT:

```
Input → Row(table)->insert(data)
↓
Validate fields
↓
Append audit fields (created_on, created_by)
↓
Apply prefix rules
↓
Resolve DB
↓
Prepare INSERT SQL
↓
Execute
↓
Return new id
```

Example for UPDATE:

```
Input → Row(table)->update(id, data)
↓
Validate id
↓
Append audit fields (updated_on, updated_by)
↓
Resolve DB
↓
Prepare UPDATE SQL
↓
Execute
↓
Return success/failure
```

---

## 6. Table Naming Rules
WebOS uses strict table naming patterns:

### 6.1 Global Naming Pattern
```
{PREFIX}_{ECOSYSTEM/APP/MODULE}_{TABLE}
```

### 6.2 Example
```
aliens_user
aliens_wallet_transaction
aeldo_order
coinpods_staking
```

### 6.3 AI Rule
AI must ALWAYS respect existing naming conventions.

---

## 7. Status Model (Critical)
WebOS uses a universal status system:

| Status | Meaning            |
|--------|--------------------|
| 1      | Active             |
| 0      | Inactive/Blocked   |
| 9      | Soft Deleted       |

### 7.1 Why Soft Delete?
- Debugging  
- Audit recovery  
- GDPR compliance  
- Avoid accidental data loss  
- Logically track entity lifecycle  

### 7.2 Row Behavior with Status
- Fetch → excludes status 9  
- Filter → excludes 9 unless overridden  
- Update → must preserve status unless changed intentionally  

---

## 8. MDBMS (Multi-Database Management System)

### 8.1 Purpose
To support millions of rows per table and multi-brand scalability.

### 8.2 How Row Engine Decides DB
Based on:
- Table family (user/table/data/log)  
- Ecosystem  
- App  
- Year/month (partition-based)  
- Tenant (if tenant-based sharding enabled)  

### 8.3 Example Logic
```
If table in {transaction, logs, analytics}
→ route to DB_2 (heavy read/write)
Else
→ route to main DB_1
```

### 8.4 AI Safe Rule
NEVER manually open DB connection.  
ALWAYS use Row Engine.

---

## 9. WebOS_Row – Enhanced Version

### 9.1 Purpose
A WebOS-aware version of Row Engine, adding:

- Ecosystem injection  
- App injection  
- Tenant awareness  
- Additional audit fields  
- Automatic meta handling  

Example extra fields:
```
ecosystem_id
app_id
tenant_id
created_by
updated_by
```

---

## 10. Debugging with $echo System

### 10.1 Purpose
Provide readable logs without breaking execution.

### 10.2 Behavior
Row functions output debug logs like:

```
$echo = 'Row.Get(): Preparing SQL...';
$echo = 'Row.Get(): Executed in 2.1ms';
$echo = 'Row.Insert(): Binding 12 fields';
```

These logs help:
- Junior developers debug faster  
- AI understand execution traces  
- System observe DB health  

---

## 11. Error Handling Model

### 11.1 Types of Errors
- Missing table  
- Invalid id  
- Empty data  
- SQL exception  
- DB connection issues  

### 11.2 Row Engine Response
- Never fatal crash  
- Always safe fallback  
- Always provide trace logs  

---

## 12. Performance Rules

### 12.1 Efficiency Requirements
Row Engine must:
- Use prepared statements  
- Cache repeated queries  
- Avoid N+1 queries  
- Support indexing recommendations  

### 12.2 Heavy Tables
Row Engine must optimize:
- transactions  
- staking  
- orders  
- analytics  
- logs  

### 12.3 AI Rule
AI should recommend:
- Index addition  
- Query refactor  
- Partitioning strategies  

---

## 13. Security Rules

### 13.1 Never expose:
- Raw SQL errors  
- Full stack trace  
- DB credentials  

### 13.2 Always:
- Sanitize inputs  
- Use parameterized queries  
- Respect user permissions  
- Add created_by/updated_by  

---

## 14. Row Extension Strategy (Adding Custom Logic)

### 14.1 When to Extend
- Special table behaviors  
- Auto-calculated fields  
- Domain-specific safety rules  

### 14.2 How to Extend
Create a file:
```
System/RowExtensions/{TableName}.php
```

Row Engine auto-loads it if:
```
table name matches extension file
```

---

## 15. Example API Usage

### 15.1 Insert
```
$User = Row('user')->Insert([
    'name' => 'Alex',
    'email' => 'x@y.com'
]);
```

### 15.2 Update
```
Row('user')->Update($UID, [
    'email' => 'new@y.com'
]);
```

### 15.3 Fetch
```
$User = Row('user')->Get($UID);
```

### 15.4 Status Change
```
Row('user')->Status($UID, 9);
```

---

## 16. AI Guidelines for Safe Row Usage

### Always:
- Use Row()  
- Follow status rules  
- Maintain naming conventions  
- Add audit fields  
- Respect ecosystem/app/tenant rules  

### Never:
- Direct SQL in Apps  
- Write CRUD in App files  
- Modify System/Row.php without order  
- Break MDBMS routing  

---

## 17. End of Document
This file defines the deepest version of the Row Engine concept.  
All future WebOS development relies on this engine executing safely, consistently, and at enterprise scale.
