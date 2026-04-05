# Database.Structure — Software Engineer Role (SSOT)

This document is the **structure entrypoint** for the Aliens database layer.

Primary detailed references:
- `Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Overview.md`
- `Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Tables.*.md`

---

## 1) Canonical DB Root
- `/Aliens/WebDB/` (workspace repo root)

DB rules apply across:
- WebOS
- WebSDK
- WebApp

---

## 2) The Only Correct DB Gateway
- Row engine (`Row()` / `WebOS_Row`)

Raw SQL is forbidden unless explicitly required.

---

## 3) Mandatory Scoping
Every DB operation must be:
- ecosystem-aware
- tenant-aware

---

## 4) Status Lifecycle
- `1` active
- `0` inactive
- `9` soft deleted

Reads must exclude `status=9` by default.

---

## 5) Indexing & Partitioning
- Partition heavy tables (monthly/yearly) using MDBMS.
- Index by:
  - `uid`, `ecosystem_id`, `tenant_id`, `status`, `created_at`

---

## 6) Cross-References
- DB overview: `Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.Overview.md`
- MDBMS routing: `Roles/Role.SoftwareEngineer/Ecosystem/Database/DB.MDBMS.Routing.md`

---

# END
