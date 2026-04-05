---
WorkflowId: WebApp_Update-Planner
WorkflowName: WebApp Update Planner
Type: Workflow_Plural
Domain: WebApp
Scope: Convert update planning artifact into executable update planner CSV
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "WebApp_Update-Planner"
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
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - /Aliens/manifest.json
ExampleFiles:
  - "/Aliens/Cyborg/AlienCyborg/Workflows/Examples/Planner/Planner_v1.0.2 .csv"
...

# [0] Purpose
`Workflow("WebApp_Update-Planner", "{AppName}", "{Description}")`

Convert `WebApp_Update-Planning` into an executable CSV planner (task list) for updates.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`

---

# [2] Dependencies (Hard Gate)
Requires update planning artifact:
- `/Aliens/.Alien/{Developer}/Planning/WebApp/{AppName}/WebApp_Update-Planning.md`

If missing:
- ErrorCode: `WF_PLANNING_REQUIRED`

---

# [3] Planner CSV Schema (SSOT)
File path (default):
- `/Aliens/Project/{Developer_Username}/{AppName}_Update_Planner.csv`

Header compatibility (NON-NEGOTIABLE):
- Planner CSV header is treated as **case-insensitive** by runners.
- Both variants are valid and MUST be supported:
  - Legacy TitleCase headers (example: `Name`, `Worklflow`, `Planning_Path`)
  - Lowercase headers (example: `name`, `worklflow`, `planning_path`).

Schema alignment rule (non-negotiable):
- Update planner schema MUST remain a strict superset-compatible match with the main `WebApp-Planner` schema.

Columns MUST match this order (SSOT):
- `ID`
- `Name`
- `utag`
- `Worklflow`
- `Parent`
- `Parent_Type`
- `Description`
- `Prority`
- `Planning`
- `Planning_Save`
- `Code`
- `Code_Save`
- `Docs`
- `Doc_Save`
- `Status`
- `Planning_Path`
- `Code_Path`
- `Doc_Path`
- `Content_Path`
- `DependsOn`
- `Ref`
- `Assign`
- `code_lines`
- `modified`
- `created`

Compatibility aliases (Runner MUST support):
- `Docs` == `doc`
- `Doc_Save` == `doc_save`

Status encoding:
- `0` = pending
- `1` = success
- `2` = failed

Critical rule:
- `Description` column blank nahi ho sakti. Yehi row ka **prompt payload** hai.

Name normalization rule (NEW; NON-NEGOTIABLE):
- `Name` me accidental duplicate segments allowed nahi:
  - `User_User` -> `User`
  - `Marketplace_Marketplace` -> `Marketplace`
  - `Repo_RepoHeader` -> `Repo_Header` (redundant prefix duplication)
- Runner rule (STRICT): agar `Name` is rule ko violate karta ho, to runner MUST:
  1) deterministically normalize `Name`
  2) same CSV me writeback kare
  3) phir paths derive/execute kare

Column notes:
- `Worklflow` / `Prority` spellings are **legacy schema** (intentional).
- `utag` = UTag HTML wrapper tag (globally unique) for UI artifacts.
  - Scope: ONLY for Screen/Section/Component update rows.
  - If row `Worklflow` is Screen/Section/Component update group => `utag` required.
  - Otherwise blank allowed.
  - Allocation SSOT: `Workflows/ACC/_Refs/WebApp_UTag.md`
  - New work MUST use UTag v2. Legacy 4/5-letter tags are update-only.
- `DependsOn` = dependency row IDs list (comma-separated). (Blank allowed.)
  - Meaning: current row MUST NOT execute until all referenced rows are completed successfully.
  - Runner rule (STRICT): before executing `Status=0` row, resolve each dependency ID:
    - If any referenced row ID does not exist => fail fast (schema/dependency gate).
    - If any referenced row has `Status != 1` => fail fast (do not execute current row).
    - Cycles are invalid; dependency graph must be acyclic.
- `Ref` = optional read-only reference pointers to understand requirements. (Blank allowed.)
  - Examples: SSOT doc path, a local file path, or a URL.
  - Runner rule: `Ref` is NOT a replacement for `Description` (prompt payload remains `Description`).
- `Assign` = developer username (task owner) (STRICT). Runner must only execute `Status=0` tasks assigned to current developer.

Progress tracking (CSV writeback; STRICT when columns exist):
- `code_lines` = is row ke code run me **sirf primary code artifact** (usually `Code_Path`) ke kitne lines create/update hue.
  - Planning/Docs/RunRecord ki lines kabhi include nahi karni.
  - If file created => `code_lines` SHOULD be total lines in that created file.
  - If file updated => `code_lines` SHOULD be diff-based count (added+modified lines; deterministic).
  - If `Code=0` => `code_lines=0`.
- `created` = ISO-8601 timestamp jab primary code file create hui (set once; overwrite forbidden).
- `modified` = ISO-8601 timestamp jab primary code file update hui (each update par refresh allowed).

Execution flags:
- `Planning/Code/Docs` are intent flags for what is needed.
- `*_Save` indicates artifact must be saved to the referenced path.

Path writeback rule (non-negotiable):
- Jab bhi koi artifact actual me write ho, uska final path hamesha CSV me save hona chahiye:
  - `Planning_Path` for planning artifacts
  - `Code_Path` for primary code artifact
  - `Doc_Path` for docs artifact
  - `Content_Path` for supporting content (CSS/JS/assets)
- `*_Save=1` ka matlab: artifact write karna AND corresponding `*_Path` fill karna mandatory hai.

Content_Path note:
- `Content_Path` sirf tab required hota hai jab row ka output actual supporting content (CSS/JS/assets/text/content) ho.
- Agar row me supporting content nahi hai, to `Content_Path` blank allowed hai.

Path prefill rule (NEW; NON-NEGOTIABLE):
- Agar update planner me `*_Path` columns exist karti hain lekin blank hain, runner MUST:
	1) deterministically derive them (no guessing)
	2) write them back to the same CSV
	3) then execute the row

Derivation SSOT:
- Use the same deterministic mapping defined in `WebApp-Planner` for base `Code_Path/Content_Path`.
- Update-planning paths MUST use the `.Update.md` conventions used by update workflows.

CSV formatting rules:
- Har cell quoted ho: `"value"`
- Multiline `Description` allowed; inner `"` -> `""`

Compatibility note (Runner):
- Quote-all preferred, but runner MUST support standard CSV where only cells containing comma/newline/quote are quoted.

---

# [4] Update Planner Rules
- IDs must be sequential integers.
- Priority is numeric; higher runs first.
- Each row must be a single executable update unit.
- `Worklflow` MUST reference existing ACC workflow ids, usually from:
  - `WebApp_Screen_Update`
  - `WebApp_Section_Update`
  - `WebApp_Component_Update`

Do NOT mix create-workflows in update planner unless explicitly required.

Planner structure recommendations:
- Row `ID=0` MUST be `__PlannerMeta__`.
- `ID>=1` rows must have enterprise-grade prompt in `Description` (see main WebApp planner prompt contract).

---

# [4.1] `Description` Prompt Contract (Update Planner)
Same contract as `WebApp-Planner`:
- `Description` is the single source-of-truth prompt for the row.
- Must include: `[OBJECTIVE]`, `[NON-NEGOTIABLE STANDARDS]`, `[CONTEXT]`, `[OUTPUT CONTRACT]`, `[RULES]`, `[ACCEPTANCE]`, `[DEPENDENCIES]`.
- If `Code=1`: must include mandatory CodeHeader + CodeFooter constraints.

---

# [5] Save Outputs
- Write the CSV to `/Aliens/Project/{Developer_Username}/{AppName}_Update_Planner.csv`.

---

# [6] Errors
- `WF_PLANNING_REQUIRED`
- `WF_PERMISSION_DENIED`
