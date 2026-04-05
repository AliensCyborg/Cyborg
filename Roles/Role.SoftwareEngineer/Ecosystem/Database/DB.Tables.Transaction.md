# DB.Tables.Transaction — Detailed Version
Location: `Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Tables.Transaction.md`

This document defines the complete, enterprise-grade, multi-ecosystem, multi-tenant, AI-first, WebOS-compatible structure for all **Transaction-related database tables** in the Aliens Ecosystem.

It ensures that all human developers and AI systems (like AlienCyborg) follow a strict standard for designing, inserting, reading, and maintaining transaction data.

---

# 1. Purpose of Transaction Tables

A **Transaction** = any irreversible event that affects:
- Money
- Credits
- Debits
- Orders
- Commissions
- Earnings
- BV (binary volume)
- User ledger
- Wallet balance
- Subscription billing
- Trade execution
- Product purchase
- ROI calculation

Transaction tables must be:
- Immutable  
- Auditable  
- Tenant-aware  
- Ecosystem-aware  
- Optimized for MDBMS  
- AI-friendly

---

# 2. Core Principles

### 2.1 Immutability  
No transaction field (except `status`) should ever be updated.

### 2.2 Multi-Ecosystem + Multi-Tenant  
Every row MUST contain:
```
ecosystem
tenant
```

### 2.3 UID-Only Model  
No numeric user IDs allowed in transaction tables.

Must use UID everywhere:
```
user_uid
from_uid
to_uid
order_uid
txn_uid
```

### 2.4 MDBMS Compatibility  
Transaction tables generate huge volumes → must be designed for:
- Monthly DB splits  
- Smart routing  
- Free from cross-database joins  
- Partition-safe layouts  

---

# 3. Standard Transaction Table Definition

Every transaction table MUST contain these columns:

```
id                 INT AUTO_INCREMENT
uid                VARCHAR(50) UNIQUE
ecosystem          VARCHAR(50)
tenant             VARCHAR(50)
app                VARCHAR(50)

user_uid           VARCHAR(50)
from_uid           VARCHAR(50)
to_uid             VARCHAR(50)

type               VARCHAR(50)
subtype            VARCHAR(50)

amount             DECIMAL(20,8)
fee                DECIMAL(20,8)
currency           VARCHAR(20)

meta_json          LONGTEXT
note               VARCHAR(255)

status             INT        (1=success, 0=pending/fail, 9=deleted)
created_at         DATETIME
updated_at         DATETIME
```

This structure is mandatory across the entire Aliens ecosystem.

---

# 4. Extended Transaction Types

### 4.1 Finance Transaction Fields

```
balance_before      DECIMAL(20,8)
balance_after       DECIMAL(20,8)
ledger_uid          VARCHAR(50)
reference_uid       VARCHAR(50)
gateway             VARCHAR(50)
txn_hash            VARCHAR(200)
```

### 4.2 Exchange/Trading Fields

```
market              VARCHAR(50)
order_uid           VARCHAR(50)
side                ENUM('buy','sell')
price               DECIMAL(20,8)
quantity            DECIMAL(20,8)
filled_quantity     DECIMAL(20,8)
is_maker            TINYINT(1)
fee_asset           VARCHAR(20)
```

### 4.3 MLM/Commission Fields

```
level               INT
sponsor_uid         VARCHAR(50)
position            VARCHAR(20)
bv                  DECIMAL(20,8)
commission          DECIMAL(20,8)
plan_uid            VARCHAR(50)
mlm_cycle           VARCHAR(50)
```

### 4.4 ROI/Earnings Fields

```
roi_percentage      DECIMAL(10,4)
roi_amount          DECIMAL(20,8)
calculation_period  VARCHAR(20)
payout_uid          VARCHAR(50)
earning_type        VARCHAR(50)
```

---

# 5. Transaction Table Index Rules

### Minimum Required Indexes:
```
INDEX(uid)
INDEX(user_uid)
INDEX(from_uid)
INDEX(to_uid)
INDEX(ecosystem)
INDEX(tenant)
INDEX(type)
INDEX(created_at)
```

### Optional Indexes (based on system):
```
INDEX(currency)
INDEX(order_uid)
INDEX(plan_uid)
```

Good indexing = top performance for millions of records.

---

# 6. MDBMS Routing Rules (Critical)

### 6.1 Automatic DB Sharding  
Transactions MUST be routed based on:

```
created_at
ecosystem
tenant
transaction_type
```

### 6.2 Per-Month DB Storage (Recommended)

Examples:
```
DB_2025_01
DB_2025_02
DB_2025_03
```

### 6.3 Table Prefixing

```
tx_wallet_transfer
tx_exchange_order
tx_mlm_commission
tx_roi_payout
```

---

# 7. Status Lifecycle

```
1 = Completed
0 = Pending / Failed
9 = Soft Deleted
```

### Only 3 statuses allowed.

---

# 8. API Rules for Transaction Tables

Every API dealing with transactions must:

- Validate amount  
- Validate UID  
- Validate balance  
- Prevent cross-tenant access  
- Use Row Engine only  
- Return standard response  
- Log everything  
- Mask sensitive data  
- Never expose internal DB IDs  

---

# 9. AI Rules (For AlienCyborg)

### When inserting:
- Always generate `uid`  
- Always fill ecosystem, tenant, app  
- Always record `balance_before` and `balance_after`  
- Never modify numeric ID fields  
- Always log using $echo blocks  

### When reading:
- Never show status 9  
- Filter by UID + tenant + ecosystem  
- Ensure user permission  

### When updating:
- Only update status  
- Never touch amount or balance  

---

# 10. Sample Insert Template

```php
$Echo = "Creating transaction..."; d($Echo);

$Row = [
  "uid"             => UID::Generate(),
  "ecosystem"       => $Eco,
  "tenant"          => $Tenant,
  "app"             => $App,

  "user_uid"        => $UserUID,
  "from_uid"        => $FromUID,
  "to_uid"          => $ToUID,
  "type"            => "wallet_transfer",
  "amount"          => $Amount,
  "currency"        => $Currency,

  "balance_before"  => $Before,
  "balance_after"   => $After,

  "meta_json"       => json_encode($Meta),
  "status"          => 1,
  "created_at"      => Now(),
  "updated_at"      => Now()
];

Row_Insert("tx_wallet_transfer", $Row);
```

---

# 11. Summary

This file defines the **official Aliens Ecosystem Transaction Table Standard**, which ensures:

- Clean consistent structure  
- Multi-tenant safety  
- Multi-ecosystem flexibility  
- Massive scale (millions of rows per month)  
- AI-first code generation  
- Perfect WebOS+SDK+Apps compatibility  

This is **mandatory** for all transaction-based systems.

---

End of File  
`DB.Tables.Transaction.md`
