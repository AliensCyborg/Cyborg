---
WorkflowId: PyOS_Engine_SQL-Planning
WorkflowName: PyOS Engine SQL - Planning
Type: Workflow_Singular-Planning
Domain: PyOS
Category: Engines
Language: SQL
Scope: "One Engine -> Planning"
Status: Draft
Version: 26.00.00
LastUpdated: 2025-12-28
EntryPoint: false
Inputs:
  - Target: "Single engine target. Example: PyOS.Cache"
  - Description: "Requirement (human language)"
Outputs:
  - PlanFile: "/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/{EngineBaseName}.plan.md"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
  - "Workflows/ACC/_Refs/ACC.Manifest.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/PyOS_Engines.md"
  - "Workflows/ACC/_Refs/PyOS_System.md"
ExampleFiles:
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.AI.Log.sql"
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.Analytics.Cohort.sql"
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.AI.Vector.sql"
  - "/Aliens/PyOS/Py.26.01.00/Unit/sql/PyOS.AI.Prompt.sql"
...
## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## [00] Goal
Given:
- Target (single PyOS engine name)
- Description (human requirements)

Generate an enterprise-grade plan that is:
- deterministic
- scoped only to the target engine SQL file
- compatible with PyOS versioning and folder structure
- safe-by-default (no destructive migrations unless explicitly requested)

## [01] Inputs & Normalization
### 1) Read Manifest
- Load `/Aliens/manifest.json` (as defined in ACC.Manifest)
- Validate: modes, approvals, allowed scopes (PyOS), allowed operations.

If manifest missing or invalid:
- Stop with a clear error.

### 2) Normalize Target
Accepted:
- `PyOS.Cache`
- `Cache` → normalize to `PyOS.Cache`

Compute:
- EngineShortName
- EngineBaseName = `PyOS.<EngineShortName>`

Validate using ACC.Targets:
- Only allowed characters
- No empty values
- No path traversal

## [02] Resolve PyOS Version & Paths
### 1) Choose PyOS Version
- Prefer latest folder under `/Aliens/PyOS/` (example: `Py.26.00.00`)
- If cannot detect: default `Py.26.00.00`

### 2) Resolve SQL Engine Path
- SQL file output path:
  - `/Aliens/PyOS/{Version}/unit/sql/{EngineBaseName}.sql`

Precondition:
- `/Aliens/PyOS/{Version}/unit/sql/` must exist (do not create installer-level folders).

If missing:
- Error.

## [03] Context Discovery
Goal: plan banate time system ko related patterns / examples dhoondhne hain.

### 1) Look for existing engine SQL examples
Search in:
- `/Aliens/PyOS/{Version}/unit/sql/`
Pick 2-4 closest examples:
- closest naming / similar category
- smaller + larger file mix (for style + scale reference)

### 2) Look for related engine implementations
Optional context references:
- `/Aliens/PyOS/{Version}/Engine/{EngineBaseName}.py` (if exists)
- `/Aliens/PyOS/{Version}/unit/js/{EngineBaseName}.js` (if exists)

Purpose:
- table names, configuration keys, cache keys, naming conventions.
Hard rule:
- planning must NOT modify these files.

### 3) Identify DB & SQL assumptions
Decide from ecosystem (if available in refs / system):
- DB dialect (MySQL/MariaDB/Postgres/etc.)
- naming conventions (snake/pascal)
- multi-tenant patterns (TenantId / AppId scoping)
If unknown:
- do not guess; write “UNKNOWN” and add a plan checkpoint that asks for confirmation via manifest or system config.

## [04] Plan Template (What to produce)
Plan must include these sections:

1) Overview
- Engine name and purpose (from Description)
- Target file path
- Intended operations (Create/Update)

2) SQL Scope
- What SQL artifacts can be created/updated:
  - tables
  - indexes
  - views
  - procedures/functions (only if PyOS supports; otherwise “Not used”)
- What is forbidden unless explicitly requested:
  - dropping tables/columns
  - destructive data changes
  - long locks on large tables

3) Idempotency & Migration Safety
- Use `IF NOT EXISTS` where possible
- Split changes into safe steps
- Backward compatibility rules (existing deployments)
- Rollback notes (even if rollback is “manual only”)

4) Performance & Scale
- Index strategy
- expected query patterns
- large data safety (millions+ rows)
- online schema change guidance (if relevant)

5) Observability & Auditability
- comment headers for sections
- change log notes
- “why” notes for each schema/index decision

6) Testing Plan
- How to validate locally
- How to validate in staging
- Minimal smoke checks

7) Open Questions (only if unavoidable)
- Only ask what is essential; otherwise mark as TODO in plan.

## [05] Save Plan
Write file:
- `/Aliens/.Alien/{Developer_Username}/Planning/PyOS/Engine/SQL/{EngineBaseName}.plan.md`

Rules:
- Create directories if they are within `.Alien/{Developer_Username}/Planning/`
- Never write outside `.Alien/Planning` for planning outputs

## [06] Done Criteria
- Plan file exists at declared path
- Plan includes all template sections
- No code file is written in this workflow
- No docs file is written in this workflow
