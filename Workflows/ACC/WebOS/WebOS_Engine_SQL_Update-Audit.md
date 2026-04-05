---
WorkflowId: WebOS_Engine_SQL_Update-Audit
WorkflowName: WebOS Engine SQL Update (Audit)
Type: Workflow_Singular-Audit
ParentWorkflowId: WebOS_Engine_SQL_Update
Domain: WebOS
Category: Engines
Language: SQL
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-05
Inputs:
  - Target: "Single engine target. Example: WebOS.Cache"
  - Description: "Update intent"
Outputs:
  Audit:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileName: "Audit.md"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebOS/Engine/SQL/"
    FileName: "{EngineBaseName}.update.audit.md"
Safety:
  NoDelete: true
  MissingTargetPolicy: "error"  # update-only audit; SQL file must exist
  ProdWrite: "no"              # audit writes only run record
  DbTouch: "not-required"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Audit.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/WebOS_Engines.md"
  - "Workflows/ACC/_Refs/WebOS_System.md"
  - "/Aliens/manifest.json"
ExampleFiles:
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Log.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.Analytics.Cohort.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Vector.sql"
  - "/Aliens/WebOS/PHP.26.01.00/Unit/sql/WebOS.AI.Prompt.sql"
...

# WebOS_Engine_SQL_Update-Audit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Audit.md`

---

## [00] Purpose
Is workflow ka kaam: **enterprise-grade audit** of an existing WebOS SQL engine file before update.

Output:
- `/Aliens/.Alien/{Developer}/Runs/{RunId}/Items/{RunItemId}/Audit.md`

Deterministic Audit Artifact (hard):
- Also write a stable copy for downstream steps:
  - `/Aliens/.Alien/{Developer}/Planning/WebOS/Engine/SQL/{EngineBaseName}.update.audit.md`

Reason (hard):
- Child workflows ka `{RunId}/{RunItemId}` different hota hai; Planning/Code must not rely on run-scoped paths.

---

## [01] Target Normalization
Accepted Targets:
- `WebOS.Cache`
- `Cache` (normalized to `WebOS.Cache`)

Forbidden:
- `.sql` extension in Target
- Path traversal (`..`)

---

## [02] Resolve SQL File
Resolve Version: latest (fallback `PHP.26.00.00`).

Resolved SQL file:
- `/Aliens/WebOS/{Version}/unit/sql/{EngineBaseName}.sql`

Hard rule:
- If SQL file missing => fail with `WF_TARGET_MISSING`.

---

## [03] Audit Scope (Hard)
Audit MUST cover (minimum):

### A) AliensStyle for SQL
- Header block comment present
- Clear section markers (TABLES/INDEXES/VIEWS/SEED/CHANGELOG)
- Change log notes for updates

### A.1) Must-Fix Items (User-Reported; Non-Negotiable)
Audit MUST explicitly detect and report these (Pass/Fail + concrete fix):
1) Header `Author` must be exactly: `AlienCyborg`
2) Footer/CHANGELOG section missing
3) Section markers missing / inconsistent
4) Comments poor (no noise; meaningful “why” comments for indexes/constraints)
5) AliensStyle/AliensGrade not applied fully
6) File too small / not enterprise-grade for its purpose (missing required sections or planned schema/change inventory)

### B) Safety (DB)
- No destructive statements by default:
  - DROP TABLE/COLUMN, TRUNCATE, mass UPDATE without WHERE
- DDL guarded where possible (`IF NOT EXISTS`, safe add)
- Large-table risk notes (locks, online schema changes)

### C) Idempotency
- Re-run safe behavior
- Upserts/seed patterns are idempotent

### D) Multi-Tenant / Isolation
- Tenant/App scoping strategy (if applicable)
- Keying/naming conventions consistent

### E) Compatibility
- WebOS version compatibility assumptions stated
- DB dialect assumptions stated (or marked UNKNOWN)

### F) Docs Drift
- Docs file path:
  - `/Aliens/Docs/WebOS/Engine/SQL/{EngineBaseName}.md`
- If present: mismatch in schema list, run instructions, safety notes

---

## [04] Audit Output Template (Mandatory)
Audit.md MUST include (order fixed):
1) Header (EngineBaseName, Version, RunId/RunItemId)
2) File Paths (sql path + docs path)
3) Summary Score (Pass/Fail + risk)
4) Violations List (grouped: Style, Safety, Idempotency, MultiTenant, Compatibility, Docs)
5) Required Fix Checklist
6) Non-Destructive Notes (what must NOT change)
7) Approval Note (if ApprovalMode=manual)

---

## [05] Minimal Success Criteria
- Audit.md exists at RunRecord path.
- If pass: explicitly states "Ready for update".
- If fail: explicitly states "Do not update until fixed" with a checklist.

Plus (hard):
- `{EngineBaseName}.update.audit.md` exists at deterministic Planning path.
