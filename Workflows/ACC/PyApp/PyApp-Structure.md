---
WorkflowId: PyApp-Structure
WorkflowName: PyApp Structure
Type: Workflow_Plural
Domain: PyApp
Scope: Create app structure (folders/files) based on Planner CSV; update planner path columns
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp-Structure"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Planner:
    SaveTo: "/Aliens/Project/{Developer_Username}/"
    FileName: "{AppName}_Planner.csv"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/"
    FileName: "PyApp-Structure.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
  - "Structure only; no business logic"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("PyApp-Structure", "{AppName}", "{Description}")`

Create the complete app structure as empty files/folders in correct locations based on the Planner CSV, and update CSV path columns.

Hard rule (non-negotiable): **Quality > Speed**.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

# [2] Dependencies (Hard Gate)
Requires planner file:
- `/Aliens/Project/{Developer_Username}/{AppName}_Planner.csv`

If missing:
- ErrorCode: `WF_PLANNER_REQUIRED`

---

# [3] Structure Rules
- Create ALL required files as empty placeholders (no content) unless the specific workflow mandates content.
- File paths must be deterministic.
- This workflow only scaffolds structure.
- Do NOT implement business logic here.

## [3.1] Canonical PyApp Root (SSOT)
Use these defaults (confirmed):
- Version: `26.00.00`
- Theme: `{AppName}_Basic`

Deterministic root:
- `AppRoot = /Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`

Minimum required scaffold folders (create if missing):
- `{AppRoot}/User/`
- `{AppRoot}/User/event`
- `{AppRoot}/User/layout`
- `{AppRoot}/User/component/`
- `{AppRoot}/User/section/`
- `{AppRoot}/User/screen/`
- `{AppRoot}/User/css/`
- `{AppRoot}/User/icon/`

Notes:
- If planner contains workflows that target `Admin/`, create `{AppRoot}/Admin/...` folders only when required by those rows.

## [3.2] CSV Path Columns (Mandatory Update)
For each CSV row, update these columns when deterministic:
- `Planning_Path`
- `Code_Path`
- `Doc_Path`
- `Content_Path`

Also maintain progress/metrics columns (mandatory when present in CSV):
- `code_lines`  (integer; last process me AI ne jitni code lines add/update ki)
- `modified`    (timestamp; primary code file modified time; ISO-8601 recommended)
- `created`     (timestamp; primary code file created time; ISO-8601 recommended)

Rules:
- `Code_Path` MUST point to the primary code artifact for that task (Python/JS/etc.) when applicable.
- `Content_Path` is used for the secondary paired file when a workflow produces two code artifacts (example: CSS).
- If a row’s paths cannot be resolved deterministically from `Worklflow` + `Name`, leave the path columns unchanged and record an error.

## [3.3] Deterministic Mapping (PyApp Screen/Section/Component)
Derive `{NameNormalized}` using the same normalization rules defined in `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`.

When `Worklflow` references one of these groups, use the mappings below.

### Screen group
Worklflow in:
- `PyApp_Screen`, `PyApp_Screen-Code`, `PyApp_Screen_Update`, `PyApp_Screen_Update-Code`

Create empty placeholders:
- `{AppRoot}/User/screen/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}-Screen.css`

Set CSV columns:
- `Code_Path = {AppRoot}/User/screen/{NameNormalized}.py`
- `Content_Path = {AppRoot}/User/css/{NameNormalized}-Screen.css`

Legacy compatibility (no rename/move; do not create legacy placeholders):
- Some older apps may already have: `{AppRoot}/User/css/{NameNormalized}.css`
- Treat legacy as fallback only; canonical is `-Screen.css`.

### Section group
Worklflow in:
- `PyApp_Section`, `PyApp_Section-Code`, `PyApp_Section_Update`, `PyApp_Section_Update-Code`

Create empty placeholders:
- `{AppRoot}/User/section/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}-Section.css`

Set CSV columns:
- `Code_Path = {AppRoot}/User/section/{NameNormalized}.py`
- `Content_Path = {AppRoot}/User/css/{NameNormalized}-Section.css`

Legacy compatibility (no rename/move; do not create legacy placeholders):
- Some older apps may already have: `{AppRoot}/User/css/{NameNormalized}.css`
- Treat legacy as fallback only; canonical is `-Section.css`.

### Component group
Worklflow in:
- `PyApp_Component`, `PyApp_Component-Code`, `PyApp_Component_Update`, `PyApp_Component_Update-Code`

Create empty placeholders:
- `{AppRoot}/User/component/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}.css`

Set CSV columns:
- `Code_Path = {AppRoot}/User/component/{NameNormalized}.py`
- `Content_Path = {AppRoot}/User/css/{NameNormalized}.css`

### Layout group
Worklflow in:
- `PyApp_Layout`, `PyApp_Layout-Code`

Create empty placeholders:
- `{AppRoot}/User/layout/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}-Layout.css` (optional; create only when planner row intends layout CSS)

Set CSV columns:
- `Code_Path = {AppRoot}/User/layout/{NameNormalized}.py`
- `Content_Path = {AppRoot}/User/css/{NameNormalized}-Layout.css` (if layout CSS intended)

### PySDK Module JS group (PyApp JS home; NON-NEGOTIABLE)
Worklflow in:
- `PySDK_Module_JS`
- `PySDK_Module_JS-Planning`
- `PySDK_Module_JS-Code`
- `PySDK_Module_JS-Documentation`

Notes:
- PyApp ke andar custom JS forbidden hai. JS ka home module-level hai.
- For this group, `Name` is the `{Module}` (no PyApp filename normalization).
- Validation: `{Module}` MUST be a simple module name (no `/`).

Do NOT create any PyApp local JS placeholders for this group.
Only update planner path columns deterministically:
- `Code_Path = /Aliens/PySDK/Py.26.00.00/Module/{Module}/js/index.js`
- `Planning_Path = /Aliens/.Alien/{Developer}/Planning/PySDK/Module/{Module}/JS/index.plan.md`
- `Doc_Path = /Aliens/Docs/PySDK/Module/{Module}/JS/index.md`
- `Content_Path` MUST remain blank for module JS rows.

### Planning / Documentation support (when present)
If `Worklflow` ends with:
- `-Planning` => set `Planning_Path` to:
  - `/Aliens/.Alien/{Developer}/Planning/PyApp/{AppName}/{Entity}/{NameNormalized}.md`
- `-Documentation` => set `Doc_Path` to the workflow’s documented output path (example for screens):
  - `/Aliens/Docs/PyApp/{AppName}/Screens/{NameNormalized}.md`

Entity mapping:
- Screen => `{Entity}=Screen`
- Section => `{Entity}=Section`
- Component => `{Entity}=Component`
- Layout => `{Entity}=Layout`

---

# [4] CSV Update Rules
For each row:
- If the row represents a planning artifact -> fill `Planning_Path`
- If the row represents a code artifact -> fill `Code_Path`
- If the row represents a docs artifact -> fill `Doc_Path`
- If the row needs raw content/reference -> fill `Content_Path`

---

# [5] Save Outputs
- Update the same CSV in-place.

---

# [6] Errors
- `WF_PLANNER_REQUIRED`
- `WF_PERMISSION_DENIED`
