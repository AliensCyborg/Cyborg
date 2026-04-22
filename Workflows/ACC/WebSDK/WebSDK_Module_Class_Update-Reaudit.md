---
WorkflowId: WebSDK_Module_Class_Update-Reaudit
WorkflowName: WebSDK Module Class Update (Reaudit)
Type: Workflow_Singular-Reaudit
ParentWorkflowId: WebSDK_Module_Class_Update
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
    # Module-scoped target (default)
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/"
    FileName: "Class.Update.reaudit.md"
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
  # The "Activity" module is the canonical baseline for CRUD function shape,
  # `$Array` flow, return structure, and `$echo` + `d()` discipline.
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Activity/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Account/php/index.php"
  - "/Aliens/WebSDK/PHP.26.00.00/Module/Withdraw/php/index.php"
---

# WebSDK_Module_Class_Update-Reaudit

## [00A] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Reaudit.md`

---

## [00] Purpose
Post-Code verification step for `WebSDK_Module_Class_Update`.

What it does (in order):
1. Re-read the `Description`, the Planning `## Required Items` block, the
   Pre-Code Audit artifact, and the **current** state of the touched
   PHP class file(s).
2. Score each `RI-NNN` as `applied | partial | missing | blocked`.
3. Enumerate `OutOfScopeChanges[]` (anything not traceable to an RI).
4. Emit a Compliance Matrix and a verdict.
5. If `noncompliant`, instruct orchestrator to loop back to Planning.

Hard rule: this step **must not modify any code file**. It is read-only.

---

## [01] Targets
Same target grammar as the parent workflow:
- `ModuleName`             => reaudit ALL php class files under module
- `ModuleName/FileBase`    => reaudit ONLY that file

Validation reuses the Audit step's rules. Missing target => `WF_TARGET_MISSING`.

---

## [02] Required Inputs (Hard)
All four MUST be present before scoring:

1. Original `Description` (from RunContext / parent workflow input).
2. Planning artifact:
   - Module target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.md`
   - File target:   `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.md`
   - Planning MUST contain a parseable `## Required Items` block.
3. Pre-Code Audit artifact:
   - Module target: `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.audit.md`
   - File target:   `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.audit.md`
4. Current code state on disk (the file(s) Code step touched).

If any input is missing => `ACC_ERR_REAUDIT_INPUT_MISSING` (action:
re-run Planning so it emits Required Items, or re-run Audit if its
artifact is missing). No code modification.

---

## [03] Verification Catalog (WebSDK Module Class baseline)
Reaudit's PRIMARY scoring source is Planning's `## Required Items`. In
addition, this workflow enforces a baseline catalog drawn from the
canonical `Activity` module (and the audit reports collected during the
2026-04 WebSDK module class update wave). These checks run as **defaults**
when Planning's RIs touch the corresponding area.

> Reaudit MUST NOT auto-add a baseline check as a "missing RI". If a
> baseline-relevant gap is found and there is no matching RI, Reaudit
> records it as `Observation` (severity `info`) and lets Planning decide
> in the next iteration whether to promote it to an RI.

### 3.1 CRUD Family Shape (Activity baseline)
For every entity exposed by the touched class, expect 6 canonical functions:

```
<Entity>UpdateNew($Array=[], $SQL=false);
<Entity>($UID=NULL, $SQL=false, $Array=[]);
<Entities>($Array=[], $NewConditions=[], $SQL=false);
<Entity>New($Array=[], $SQL=false);
<Entity>Update($Array=[], $NewConditions=[], $SQL=false);
<Entity>Status($UID=NULL, $Status=NULL, $SQL=false, $Array=[]);
```

Checks:
- Signature shape matches (parameter names, defaults, order).
- `$Array` parameter, where accepted, is **actually used** inside the body.
- `$SQL` is forwarded to the underlying `Row*` family; never hardcoded `true`.

### 3.2 `$Array` Forwarding Discipline
- `<Entity>($UID, $SQL, $Array)` MUST forward `$Array` to `Row($UID, $Table, $SQL, $Array)`.
- `<Entity>Status(...)` MUST forward `$Array` to `RowStatus(..., $Array)`.
- Functions that accept `$Array` and never reference it again => `partial`/`missing`.

### 3.3 Result Verification on Helper Calls
Every call to `RowNew | RowUpdate | RowStatus | UID_Encode` MUST be followed
by an explicit success/failure check before returning success. Blind
returns are flagged.

### 3.4 UID Handling
- `UID_Encode([...])` returns a structured array. Code MUST extract the
  `'uid'` key, NOT assign the whole array to `$Array['uid']`.
- Existing `$Array['uid']` MUST NOT be silently overwritten unless an RI
  authorizes it.

### 3.5 Return Structure
- Success and error paths MUST return the same shape. The agreed shape
  uses `s | c | t | o | SQL | connect | starttime | endtime` (per Activity
  baseline). Inconsistent shape => `partial`.
- `starttime` and `endtime` MUST be present on every exit.

### 3.6 `$echo` + `d()` Discipline
- `$echo` is built as `'[Module][Method] Message'` and immediately passed
  to `d($echo, __LINE__, 'process|success|error')`.
- Every public method has Start + End (Completed/Failed) logs on every
  exit path. Loop bodies include iteration context.
- Logs MUST NOT print secrets.

### 3.7 Spelling / Naming Hygiene
- `oprator` => `operator` (regression flagged in 2026-04 audit).
- `Historys` => `Histories` (English plural rule).
- These are flagged only when they appear in code touched by the current
  Code run, AND only as `Observation` unless an RI authorizes a fix.

### 3.8 Pattern Fidelity to Reference
- Touched class methods MUST mirror the corresponding Activity-baseline
  method's high-level structure (Start log -> Validate -> Helper call ->
  Result check -> End log -> single return).

---

## [04] Deterministic Artifact Naming
- Module target =>
  `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.reaudit.md`
- File target =>
  `/Aliens/.Alien/{Developer_Username}/Planning/WebSDK/Module/{ModuleName}/Class.Update.{FileBase}.reaudit.md`

Both MUST be written every iteration (append-only iteration history; do not
overwrite previous iteration body — append a new `## Iteration N` section).

---

## [05] Compliance Matrix Template (Mandatory)

```
# Reaudit — {ModuleName}{/FileBase}

- Workflow: WebSDK_Module_Class_Update-Reaudit
- Run: {RunId} / {RunItemId}
- Iteration: {N}
- Verdict: compliant | noncompliant
- Author: AlienCyborg

## Inputs
- Description: present
- Planning Required Items: {count}
- Pre-Code Audit: present
- Code state: read OK

## Compliance Matrix
| RI     | Status   | Evidence (file:line or method)        | Gap / Note |
|--------|----------|----------------------------------------|------------|
| RI-001 | applied  | Address.php :: Address_HistoryStatus   | -          |
| RI-002 | partial  | Address.php :: Address_HistoryNew      | $Array not forwarded to RowNew |
| RI-003 | missing  | (not found in Code diff)               | needs Code re-run |

## Out-of-Scope Changes
- file: Address.php
  change: renamed `Historys` -> `Histories`
  severity: warning
  recommendation: revert (not in any RI) OR move_to_planning_then_code

## Observations (informational; not part of verdict)
- spelling: `oprator` still present in 3 callsites (no RI to fix it this run)

## Loop Instruction (only if noncompliant)
- Re-open Planning artifact at section "Step-by-Step Plan"
- Refine Required Items: RI-002 acceptance is missing forwarding clause
- Add new RI for RI-003 result verification
- Then re-run Code, then re-run Reaudit (Iteration N+1)
```

A machine-readable `Reaudit.json` MUST also be written with the same
fields (matrix as array of objects).

---

## [06] Verdict Rules
- `compliant`     iff every RI is `applied` AND no `OutOfScopeChanges[]`
                  entry is severity `violation`.
- `noncompliant`  otherwise.

Severity policy for `OutOfScopeChanges`:
- `info`      : negligible (e.g., whitespace inside touched section).
- `warning`   : noticeable but non-functional (e.g., comment rewording not asked).
- `violation` : functional change not traced to any RI (rename, signature
                change, removed branch, added side-effect, etc.).

A single `violation` is enough to make the verdict `noncompliant`.

---

## [07] Loop Back Rule (Strict)
On `noncompliant`:
- Orchestrator MUST loop to `WebSDK_Module_Class_Update-Planning` (not Code).
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
noncompliant) loop instruction emitted. Reaudit "passing" only means
the verification ran cleanly; the parent workflow uses the verdict to
decide next step.

## Changelog
- 26.00.00 (2026-04-18): Initial Reaudit child for WebSDK_Module_Class_Update.
  Embeds Activity-baseline verification catalog drawn from the 2026-04
  WebSDK Module Class Update audit reports. Author: AlienCyborg.
