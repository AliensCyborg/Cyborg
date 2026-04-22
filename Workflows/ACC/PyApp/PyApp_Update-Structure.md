---
WorkflowId: PyApp_Update-Structure
WorkflowName: PyApp Update Structure
Type: Workflow_Plural
Domain: PyApp
Scope: Ensure required structure exists for updates (modify-only; no deletes)
Status: Draft
Version: 26.00.00
LastUpdated: 2026-01-20
Inputs:
  Workflow():
    - WorkflowId: "PyApp_Update-Structure"
    - Targets: "AppName"
    - Description: "REQUIRED"
Outputs:
  Planner:
    SaveTo: "/Aliens/Project/{Developer_Username}/"
    FileName: "{AppName}_Update_Planner.csv"
  Artifacts:
    SaveTo: "/Aliens/.Alien/{Developer_Username}/Planning/PyApp/{AppName}/"
    FileName: "PyApp_Update-Structure.md"
Safety:
  NoDelete: true
  ProdWrite: "no"
  DbTouch: "not-required"
NonNegotiables:
  - "DescriptionRequired"
  - "Quality > Speed"
  - "Modify-only; no deletes"
Requires:
  - Workflows/ACC/_README.md
Refs:
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - /Aliens/manifest.json
ExampleFiles: []
...

# [0] Purpose
`Workflow("PyApp_Update-Structure", "{AppName}", "{Description}")`

Validate update targets exist and update the CSV path columns deterministically.

Hard rule (non-negotiable): **Quality > Speed**.

Important (update-only):
- This workflow must NOT create placeholders for missing targets.
- Missing target files MUST be treated as errors.

---

# [1] Mandatory Includes (Order)
- `Workflows/ACC/_README.md`
- `Workflows/ACC/_Refs/ACC.Paths.md`
- `Workflows/ACC/_Refs/ACC.Safety.md`

---

# [2] Dependencies (Hard Gate)
Requires update planner file:
- `/Aliens/Project/{Developer_Username}/{AppName}_Update_Planner.csv`

If missing:
- ErrorCode: `WF_PLANNER_REQUIRED`

---

# [3] Deterministic Root (SSOT)
Use these defaults:
- Version: `26.00.00`
- Theme: `{AppName}_Basic`

Deterministic root:
- `AppRoot = /Aliens/PyApp/{AppName}/26.00.00/theme/{AppName}_Basic/`

If AppRoot missing:
- ErrorCode: `WF_APPROOT_NOT_FOUND`
- Action: run `PyApp_Structure` (create app) OR fix the app version/theme path

---

# [4] CSV Path Columns (Mandatory Update)
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
- `Code_Path` MUST point to the primary Python file for that task (when applicable).
- `Content_Path` is used for the secondary paired file when a workflow produces two code artifacts (example: CSS).

---

# [5] Update-only Validation Rules
For workflow groups:

## Screen update group
Worklflow in:
- `PyApp_Screen_Update`, `PyApp_Screen_Update-Code`

Resolve:
- `{AppRoot}/User/screen/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}-Screen.css`        (canonical)
- `{AppRoot}/User/css/{NameNormalized}.css`               (legacy transitional)
- `{AppRoot}/User/css/{NameLegacyUnderscore}-Screen.css`  (legacy)
- `{AppRoot}/User/css/{NameLegacyUnderscore}.css`         (legacy)

Validate:
- If either file missing => error `WF_SCREEN_NOT_FOUND`

## Section update group
Worklflow in:
- `PyApp_Section_Update`, `PyApp_Section_Update-Code`

Resolve:
- `{AppRoot}/User/section/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}-Section.css`        (canonical)
- `{AppRoot}/User/css/{NameNormalized}.css`               (legacy transitional)
- `{AppRoot}/User/css/{NameLegacyUnderscore}-Section.css` (legacy)
- `{AppRoot}/User/css/{NameLegacyUnderscore}.css`         (legacy)

Validate:
- If either file missing => error `WF_SECTION_NOT_FOUND`

## Component update group
Worklflow in:
- `PyApp_Component_Update`, `PyApp_Component_Update-Code`

Resolve:
- `{AppRoot}/User/component/{NameNormalized}.py`
- `{AppRoot}/User/css/{NameNormalized}.css`

Validate:
- If either file missing => error `WF_COMPONENT_NOT_FOUND`

Name normalization SSOT:
- `Workflows/ACC/_Refs/PyApp_Naming_and_Screens.md`

---

# [6] Save Outputs
- Update the same CSV in-place.

---

# [7] Errors
- `WF_PLANNER_REQUIRED`
- `WF_APPROOT_NOT_FOUND`
- `WF_SCREEN_NOT_FOUND`
- `WF_SECTION_NOT_FOUND`
- `WF_COMPONENT_NOT_FOUND`
- `WF_PERMISSION_DENIED`
