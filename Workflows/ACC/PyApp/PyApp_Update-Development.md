---
WorkflowId: PyApp_Update-Development
WorkflowName: PyApp Update Development
Type: Workflow_Plural
Domain: PyApp
Scope: Execute update planner CSV tasks and update status codes
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Update-Development"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Planner:
    SaveTo: "/Aliens/Project/{Developer_Username}/"
    FileName: "{AppName}_Update_Planner.csv"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
Requires:
  - Workflows/ACC/_README.md
  - Workflows/ACC/_Common/Workflow_Plural.md
Refs:
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("PyApp_Update-Development", "{AppName}", "{Description}")`

Read the Update Planner CSV and execute update tasks in priority order, updating status codes.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Common/Workflow_Plural.md`

---

# [2] Dependencies (Hard Gate)
Requires update planner CSV:
- `/Aliens/Project/{Developer_Username}/{AppName}_Update_Planner.csv`

If missing:
- ErrorCode: `WF_STRUCTURE_REQUIRED`

---

# [3] Execution Rules
- Sort tasks by `Prority` (desc), then by `ID` (asc).
- Only execute tasks with `Status = 0`.
- For each task:
  - CSV schema must be valid. If required columns missing => fail with `WF_TASK_SCHEMA_INVALID`.
  - Dependency gate (STRICT):
    - If `Task.DependsOn` is non-empty, parse it as comma-separated row IDs.
    - For each dependency ID:
      - If referenced row ID does not exist => fail with `WF_TASK_DEPENDENCY_NOT_MET`.
      - If referenced row `Status != 1` => fail with `WF_TASK_DEPENDENCY_NOT_MET`.
    - Do not execute the task until dependencies are green.
  - `Task.Assign` MUST be non-empty.
    - If empty => fail with `WF_TASK_ASSIGNMENT_REQUIRED`.
  - Current developer MUST match `Task.Assign`.
    - Developer username must be resolved via `ACC.Manifest` (`Developer.Username`).
    - If mismatch => fail with `WF_TASK_NOT_ASSIGNED` (do NOT execute unassigned task).
  - If `Task.Worklflow` is a Screen/Section/Component update group, then `Task.utag` MUST be non-empty.
    - (`ID=0` meta row may be blank.)
    - If empty => fail with `WF_TASK_SCHEMA_INVALID`.
  - `Task.Description` MUST be non-empty (yehi prompt hai). If empty => fail with `WF_TASK_DESCRIPTION_REQUIRED`.
  - If `Task.Ref` is present:
    - Treat it as read-only requirement context (SSOT docs/local file paths/URLs).
    - Keep `Task.Description` as the single prompt payload; do not rewrite Description.
    - Include `Ref` in the run summary for traceability.
  - call `Workflow(Task.Worklflow, Task.Name, Task.Description)`
  - if success -> `Status=1` and perform CSV writeback (below)
  - if failed -> `Status=2` and stop (do not continue)

CSV writeback after a successful task (non-negotiable):
- Paths: whenever an artifact is written, its final path MUST be written back into the row:
  - `Planning_Path` for planning artifacts
  - `Code_Path` for primary code artifact
  - `Doc_Path` for docs artifact
  - `Content_Path` for supporting content (CSS/JS/assets)
- Progress tracking (when columns exist):
  - `code_lines` MUST be updated **only for the primary code artifact** (usually `Code_Path`).
    - Do NOT include Planning/Docs/RunRecord lines.
    - If file created => set `code_lines` to total lines in created file.
    - If file updated => set `code_lines` to diff-based line count (added+modified; deterministic).
    - If `Task.Code != 1` => `code_lines=0`.
  - `created` / `modified` timestamps (ISO-8601, UTC recommended):
    - If primary code file was created in this task => set `created=now` (set-once; never overwrite) and leave `modified` unchanged.
    - If primary code file existed and was modified => set `modified=now`.

---

# [4] Save Outputs
- Update CSV in-place after each task status change.
- Write run summary under:
  - `/Aliens/.Alien/{Developer}/Runs/PyApp/{AppName}/PyApp_Update-Development.Run.md`

---

# [5] Errors
- `WF_STRUCTURE_REQUIRED`
- `WF_TASK_FAILED`
- `WF_TASK_DESCRIPTION_REQUIRED` *(maps to ACC ref: `ACC_ERR_CSV_DESCRIPTION_MISSING`)*
- `WF_TASK_SCHEMA_INVALID` *(maps to ACC ref: `ACC_ERR_CSV_SCHEMA_INVALID`)*
- `WF_TASK_DEPENDENCY_NOT_MET` *(maps to ACC ref: `ACC_ERR_TASK_DEPENDENCY_NOT_MET`)*
- `WF_TASK_ASSIGNMENT_REQUIRED` *(maps to ACC ref: `ACC_ERR_TASK_ASSIGNMENT_REQUIRED`)*
- `WF_TASK_NOT_ASSIGNED` *(maps to ACC ref: `ACC_ERR_TASK_NOT_ASSIGNED`)*
