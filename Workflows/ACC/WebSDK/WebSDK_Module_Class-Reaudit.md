---
WorkflowId: WebSDK_Module_Class-Reaudit
WorkflowName: WebSDK Module Class (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_Class
Domain: WebSDK
TargetKind: "Module/Class"
Status: Stable
Version: 26.00.00
LastUpdated: 2026-04-18
Author: AlienCyborg
Inputs:
  - Targets
  - Description
Outputs:
  RunRecord:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/"
    FileNames:
      - "Reaudit.md"
      - "Reaudit.json"
  DeterministicArtifact:
    # Module-scoped target (default). Note the filename differs from the
    # Update-Reaudit artifact so create vs update audit trails do not clash.
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/"
    FileName: "Class.reaudit.md"
    # File-scoped target rule documented in body (section [04]).
Safety:
  NoDelete: true
  ReadOnlyOnCode: true
  ProdWrite: "no"
  DbTouch: "not-required"
  MissingTargetPolicy: "error"
Requires:
  - "Workflows/ACC/_Common/Workflow_Universal.md"
  - "Workflows/ACC/_Common/Workflow_Singular.md"
  - "Workflows/ACC/_Common/Workflow_Singular-Reaudit.md"
  - "Workflows/ACC/_Refs/ACC.Targets.md"
  - "Workflows/ACC/_Refs/ACC.Paths.md"
  - "Workflows/ACC/_Refs/ACC.Output.md"
  - "Workflows/ACC/_Refs/ACC.Safety.md"
  - "Workflows/ACC/_Refs/ACC.Errors.md"
  - "Workflows/ACC/_Refs/WebSDK.md"
  - "/Aliens/manifest.json"
ReferenceBaseline:
  # Same canonical baselines used by the Update-Reaudit; create-mode Reaudit
  # verifies the freshly generated class meets these standards.
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Activity/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Account/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Withdraw/php/index.php"
---

# WebSDK_Module_Class-Reaudit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Reaudit.md`

---

## [00] Purpose
Post-Code verification step for `WebSDK_Module_Class` (Create scenario).

When a new module primary class is generated (or a new part class is wired into
an existing module), this step verifies — **before any documentation is
written** — that the new file actually satisfies the Required Items derived
from the user's `Description` and that no out-of-scope changes leaked in.

Why a separate Reaudit for create (in addition to the Update one):
- Update-Reaudit assumes a pre-existing file and a pre-Code Audit artifact.
- Create scenarios usually have NO pre-Code Audit (the file did not exist).
- This child uses the Planning artifact + the freshly written file as evidence,
  and adds explicit "fresh class baseline" checks.

Hard rule: this step **must not modify any code file**. It is read-only.

---

## [01] Targets
Same target grammar as the parent workflow:
- `ModuleName`              => reaudit the freshly created/touched module class file(s)
                               under `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/`
- `ModuleName/FileBase`     => reaudit ONLY that newly created part file:
                               `/Aliens/WebSDK/{LatestVersion}/Module/{ModuleName}/php/{FileBase}.php`

Validation:
- `ModuleName` and `FileBase` MUST match `^[A-Za-z0-9_]+$`.
- `FileBase` MUST NOT include `.php`.
- Singular Entity Name Rule (SSOT): violation => `ACC_ERR_NAME_MUST_BE_SINGULAR`.
- Target file(s) MUST exist on disk by the time this workflow runs (Code step
  must have produced them). Missing => `WF_TARGET_MISSING`.

---

## [02] Required Inputs (Hard)
Three of the four inputs from the common Reaudit contract apply here. The
"pre-Code Audit artifact" input does NOT apply to a fresh-create flow.

1. Original `Description` (from RunContext / parent workflow input).
2. Planning artifact:
   - Module target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.md`
   - File target:   `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.{FileBase}.md`
   - Planning MUST contain a parseable `## Required Items` block
     (see `Workflow_Singular-Planning.md` -> "Required Items Extraction").
3. Current code state on disk (the file(s) the Code step wrote).

If any of the above is missing/unreadable => `ACC_ERR_REAUDIT_INPUT_MISSING`.
No code modification.

> Note: if `Description` is empty for a Create call (allowed by parent's input
> contract), Planning MUST still emit a `## Required Items` block driven by the
> baseline catalog below; Reaudit then scores against that baseline-only RI set.

---

## [03] Verification Catalog (Fresh Class Baseline)
Reaudit's PRIMARY scoring source is Planning's `## Required Items`. In addition,
this workflow enforces a "fresh class baseline" catalog for newly created
files. Baseline checks run by default; gaps without a matching RI are recorded
as `Observation` (severity `info`) — NEVER auto-promoted to "missing RI".

### 3.1 File Skeleton (Mandatory on a fresh class file)
- Exactly one `<?php` at file start. No duplicate open tags. No `?>` close tag.
- `declare(strict_types=1);` then `Access();` near top (canonical ordering).
- Full AliensStyle file CodeHeader block present:
  - First line of header banner exactly: `# Copyright (c) A|iens. All rights reserved.`
  - One blank line, then structured header (Name, Version, Author, Author URI,
    Requires, Description, Responsibilities, Dependencies, Integration Notes).
- Author MUST be exactly `AlienCyborg`.
- File CodeFooter present (versioning notes, extension hooks, future compat).

### 3.2 Class + Manifest
- Exactly one primary `class {ModuleName}` defined in `php/index.php`
  (or one `class {ModuleName}_{Part}` for a part file).
- `$PHP['{Module}']` metadata present at top-level for `index.php`.
- `$PHP['{Module}']['function'][N]` manifest entries enumerate every public
  signature defined in the class. Mismatch (signature defined but not in
  manifest, or manifest entry without signature) => `partial`.

### 3.3 CRUD Family Shape (Activity baseline)
For every entity exposed by the class, expect 6 canonical functions:

```
<Entity>UpdateNew($Array=[], $SQL=false);
<Entity>($UID=NULL, $SQL=false, $Array=[]);
<Entities>($Array=[], $NewConditions=[], $SQL=false);
<Entity>New($Array=[], $SQL=false);
<Entity>Update($Array=[], $NewConditions=[], $SQL=false);
<Entity>Status($UID=NULL, $Status=NULL, $SQL=false, $Array=[]);
```

Checks (only when an RI declares CRUD scope, OR when this is a fresh
create of a class whose Description implies a domain entity):
- Signature shape matches (parameter names, defaults, order).
- `$Array` parameter, where accepted, is **actually used** inside the body.
- `$SQL` is forwarded to the underlying `Row*` family; never hardcoded `true`.

### 3.4 `$Array` Forwarding Discipline
- `<Entity>($UID, $SQL, $Array)` MUST forward `$Array` to `Row($UID, $Table, $SQL, $Array)`.
- `<Entity>Status(...)` MUST forward `$Array` to `RowStatus(..., $Array)`.
- Functions that accept `$Array` and never reference it again => `partial`/`missing`.

### 3.5 Result Verification on Helper Calls
Every call to `RowNew | RowUpdate | RowStatus | UID_Encode` MUST be followed
by an explicit success/failure check before returning success. Blind returns
are flagged.

### 3.6 UID Handling
- `UID_Encode([...])` returns a structured array. Code MUST extract the
  `'uid'` key, NOT assign the whole array to `$Array['uid']`.
- Existing `$Array['uid']` MUST NOT be silently overwritten unless an RI
  authorizes it.

### 3.7 Return Structure
- Success and error paths MUST return the same shape (Activity baseline:
  `s | c | t | o | SQL | connect | starttime | endtime`).
- `starttime` and `endtime` MUST be present on every exit.

### 3.8 `$echo` + `d()` Discipline
- `$echo` is built as `'[Module][Method] Message'` and immediately passed to
  `d($echo, __LINE__, 'process|success|error')`.
- Every public method has Start + End (Completed/Failed) logs on every exit
  path. Loop bodies include iteration context.
- Logs MUST NOT print secrets.

### 3.9 Section Structure + Function Code-Headers
- Every public method preceded by a SECTION divider banner.
- Every public method has a function-level CodeHeader block
  (Purpose / Input / Output / Dependencies + Developer + LastTouched date).

### 3.10 Naming Hygiene (Singular SSOT)
- ModuleName, ClassName, derived table names MUST be singular.
- English plural rules respected for collection names (`Histories`, not `Historys`).
- Spelling check: known traps like `oprator` => `operator` are flagged
  (only as `Observation` unless an RI authorizes a fix).

### 3.11 Part-File Wiring (only when target is `ModuleName/FileBase`)
- The new part file class is `class {ModuleName}_{FileBase}` (PascalCase ok).
- `php/index.php` is wired to load the part:
  - `require_once __DIR__ . '/{FileBase}.php';` present, OR
  - equivalent ecosystem loader entry present (manifest function entry pointing
    to the part class).
- Part class follows the same File Skeleton + AliensStyle rules as 3.1–3.9.

---

## [04] Deterministic Artifact Naming
- Module target =>
  `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.reaudit.md`
- File target =>
  `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.{FileBase}.reaudit.md`

Both MUST be written every iteration (append-only iteration history; do not
overwrite previous iteration body — append a new `## Iteration N` section).

> Filename intentionally uses `Class.reaudit.md` (no `Update.` segment) so
> create-mode and update-mode Reaudit artifacts coexist without collisions.

---

## [05] Compliance Matrix Template (Mandatory)

```
# Reaudit (Create) — {ModuleName}{/FileBase}

- Workflow: WebSDK_Module_Class-Reaudit
- Run: {RunId} / {RunItemId}
- Iteration: {N}
- Verdict: compliant | noncompliant
- Author: AlienCyborg

## Inputs
- Description: present | empty (baseline-only mode)
- Planning Required Items: {count}
- Code state: read OK

## Compliance Matrix
| RI     | Status   | Evidence (file:line or method)            | Gap / Note |
|--------|----------|--------------------------------------------|------------|
| RI-001 | applied  | User.php :: header banner + Author check   | -          |
| RI-002 | partial  | User.php :: UserNew                        | UID_Encode response not extracted |
| RI-003 | missing  | (not found in generated file)              | needs Code re-run |

## Out-of-Scope Changes
- file: User.php
  change: added unrequested helper `UserHelperX()`
  severity: violation
  recommendation: revert (not in any RI) OR move_to_planning_then_code

## Observations (informational; not part of verdict)
- spelling: `oprator` present in 1 callsite (no RI to fix it this run)

## Loop Instruction (only if noncompliant)
- Re-open Planning artifact at section "Step-by-Step Plan"
- Refine Required Items: RI-002 acceptance is missing UID_Encode extraction clause
- Add new RI for RI-003 baseline check
- Then re-run Code, then re-run Reaudit (Iteration N+1)
```

A machine-readable `Reaudit.json` MUST also be written with the same fields
(matrix as array of objects).

---

## [06] Verdict Rules
- `compliant`     iff every RI is `applied` AND no `OutOfScopeChanges[]`
                  entry is severity `violation`.
- `noncompliant`  otherwise.

Severity policy for `OutOfScopeChanges`:
- `info`      : negligible (e.g., whitespace inside generated header).
- `warning`   : noticeable but non-functional (e.g., extra comment not asked).
- `violation` : functional addition not traced to any RI (extra method, extra
                manifest entry, extra side-effect, extra include, etc.).

A single `violation` is enough to make the verdict `noncompliant`.

---

## [07] Loop Back Rule (Strict)
On `noncompliant`:
- Orchestrator MUST loop to `WebSDK_Module_Class-Planning` (not Code).
- Planning's next iteration MUST consume the latest `Reaudit.md` and refine
  Required Items + Out-of-Scope blocks accordingly.
- Code then re-runs, then Reaudit re-runs (Iteration N+1).

No iteration limit by default (`MaxReauditIterations: 0` in parent YAML).

---

## [08] Errors
- `ACC_ERR_REAUDIT_INPUT_MISSING`
- `ACC_ERR_REAUDIT_NONCOMPLIANCE`
- `ACC_ERR_REAUDIT_MAX_ITERATIONS` (only when parent sets a positive bound)
- `ACC_ERR_SCOPE_DEVIATION` (a violation persisted across iterations without approval)

---

## [09] Acceptance Criteria
Reaudit success = Compliance Matrix written + verdict computed + (when
noncompliant) loop instruction emitted. Reaudit "passing" only means the
verification ran cleanly; the parent workflow uses the verdict to decide
the next step.

## Changelog
- 26.00.00 (2026-04-18): Initial Reaudit child for `WebSDK_Module_Class`
  (create scenario). Verifies freshly generated class file(s) against the
  Activity-baseline catalog before Documentation runs. Author: AlienCyborg.
