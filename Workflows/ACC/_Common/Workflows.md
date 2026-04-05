---
WorkflowId: Workflows
Type: Workflow_Plural
Domain: Common
Scope: Universal planner runner (dispatch rows to child workflows)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-15
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Errors.md
  - Workflows/ACC/_Refs/ACC.Output.md
Inputs:
  Workflow():
    - WorkflowId: "Workflows"
    - Targets: "Tasks.csv (or any planner csv name)"
    - Description: "REQUIRED; run context / constraints"
Outputs:
  - "Executes exactly one runnable row from the planner per run"
  - "Writes back deterministic progress fields to the same CSV"
NonNegotiables:
  - "SingleRowPerRun (Status=0)"
  - "AssignmentGate (Assign == Developer_Username)"
  - "DependsOnGate (Status==1)"
  - "DescriptionRequired (Workflow + Row)"
  - "No delete operations"
...

# [0] Purpose
`Workflows` is a **universal planner runner**.

Goal:
- Planner file ko instruction file treat karna.
- Har run me **sirf 1** runnable row execute karna.
- Row ke `Worklflow` ko call karna, aur row ka `Description` hamesha prompt payload hona.

---

# [1] Runnable Row Definition (STRICT)
A row is runnable only if:
- `Status == 0`
- `Assign == {Developer_Username}`
- `DependsOn` empty OR all dependency rows have `Status == 1`

If no runnable row exists:
- Run returns noop/blocked (no destructive changes).

---

# [2] Dispatch Rule
For the selected row:
- Call: `Workflow(Row.Worklflow, Row.Name, Row.Description)`
- Always pass `Row.Description` (required)

Pre-dispatch path prefill (NEW; NON-NEGOTIABLE):
- If planner contains path columns (`Planning_Path`, `Code_Path`, `Doc_Path`, `Content_Path`) and any are blank:
  - Deterministically derive paths as per the planner schema SSOT (e.g. `WebApp-Planner` / `WebApp_Update-Planner`).
  - Write them back to the same CSV before dispatch.

---

# [3] Writeback Rule (Planner CSV)
After execution (success/failure), write back:
- `Status`
- `created` (set once)
- `modified` (update on each run touching the row)
- `*_Path` fields when deterministically resolvable
- `code_lines` for the primary code artifact only

## 3.0 CSV Location (SSOT)
When invoked with a CSV filename, resolve ONLY from:
- `/Aliens/Project/{Developer_Username}/{FILENAME}.csv`

No other folder is allowed.

## 3.1 Status Semantics (CRITICAL)
`Status` ka matlab **row complete** hai — phase complete nahi.

Rules:
- `Status = 0` => row pending (including multi-phase workflows where planning/code done but docs pending)
- `Status = 1` => row fully completed (terminal)
- `Status = 2` => row failed (auto retry forbidden; user must reset to 0 if they want retry)

Example (WebApp workflows):
- Run-1 (Planning) success => `Planning=1`, `Status` stays `0`
- Run-2 (Code) success => `Code=1`, `Status` stays `0`
- Run-3 (Documentation) success => `Docs=1`, `Status` becomes `1`

Iska purpose: `Workflows` ko true loop-runner banana so woh **same row** ko next run me dobara pick kar sake until completion.

## 3.2 Progress Columns (Optional but Recommended)
Planner me yeh columns present hon to runner unko phase-wise fill karega:
- `Planning`, `Planning_Save`
- `Code`, `Code_Save`
- `Docs`, `Doc_Save`

These are **progress markers** only; completion is still controlled by `Status`.

---

# [4] Child Chain (Default Order)
`Workflows` ka apna fixed child-chain conceptually required nahi.

Is workflow ka kaam sirf planner row ko dispatch karna hai.
Actual Planning → Code → Documentation chain us workflow me run hoti hai jo row ke `Worklflow` column me aata hai (e.g. `WebApp_Component-Planning` / `WebApp_Component-Code` / `WebApp_Component-Documentation`).

Note:
- Legacy compatibility ke liye `_Common/Workflows-Planning.md`, `_Common/Workflows-Code.md`, `_Common/Workflows-Documentation.md` repo me exist kar sakti hain.
- Yeh files **Deprecated** hain; dispatcher unko use nahi karta.

---

# [5] Loop Runner Behavior (STRICT)
`Workflows` ek **scheduler / loop runner** hai:
- Har run me max 1 runnable row execute hoti hai.
- Agar selected row multi-phase workflow chalata hai, to next run me same row phir se pick ho sakti hai (jab tak `Status=1` nahi ho jata).

Row selection deterministic hoti hai:
- File order me pehli row jo runnable ho, wahi pick hogi.
- Random selection forbidden.

---

# [6] Description Rules (NON-NEGOTIABLE)
Do levels par Description strict hai:

1) `Workflow('Workflows', 'Tasks.csv', Description)`
- `Description` empty/missing => fail fast

2) Planner row `Description`
- Row `Description` empty/missing => fail fast with `ACC_ERR_CSV_DESCRIPTION_MISSING`
- Do NOT mutate row progress: `Status`, `created`, `modified` ko touch mat karo (row pending hi rahe jab tak Description fill na ho)

Reason:
- Row Description **prompt payload** hai jo AI ko (GitHub Copilot) bhejna mandatory hai.
- Is rule ko file size ya workflow complexity ki wajah se kabhi relax nahi karna.

---

# [7] Assignment Gate (STRICT)
Row runnable tabhi hai jab:
- `Assign == {Developer_Username}`

`{Developer_Username}` source:
- Prefer `/Aliens/manifest.json` -> `Developer.Username`
- Fallback: developer folder name (e.g. `AnilNayak`)

---

# [8] DependsOn Gate (STRICT)
If `DependsOn` present:
- It must reference existing `ID` values in the same planner.
- All referenced dependency rows must have `Status == 1`.

If dependencies are not met:
- No row is executed.
- Run returns as blocked/no-op (no destructive work).

---

# [9] No Runnable Row Outcomes
If no runnable row exists:
- `noop`: no pending rows assigned to this developer
- `blocked`: pending rows exist but are blocked by `DependsOn`

Both outcomes are non-destructive.

---

# [10] Determinism + Safety
- One-row-per-run governance is non-negotiable.
- No delete operations.
- CSV writeback must not drop unknown columns.

CSV compatibility (NON-NEGOTIABLE):
- Header matching is case-insensitive.
- Runner MUST support common aliases used by large planners:
  - `Docs` == `doc`
  - `Doc_Save` == `doc_save`
