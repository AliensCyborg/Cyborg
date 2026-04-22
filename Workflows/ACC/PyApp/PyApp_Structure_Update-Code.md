---
WorkflowId: PyApp_Structure_Update-Code
Type: Workflow_Singular-Code
Domain: PyApp
ParentWorkflowId: PyApp_Structure_Update
Status: Draft
Version: 26.00.03
LastUpdated: 2026-01-19
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
NonNegotiables:
  - "No deletes"
...

# [0] Purpose
Missing folders/files create karna; existing ko intact rakhna.

Hard rules:
- If any of these are missing, create them with minimal bootstrapping content (do not add business logic, do not overwrite existing):
  - `User/index.py`
  - `User/layout.py`
  - `User/css.py`
  - `User/js.py`
  - `User/component.py`
  - `User/css/0.css` (must contain `:root{...}` variables + shared global CSS; variable names are SSOT and must not be renamed)
    - Required variable keys (minimum set):
      - `--0`
      - `--a`, `--aa`, `--aat`, `--aaa`, `--lgaa`, `--at`
      - `--b`, `--bt`, `--b-t`, `--tb`
      - `--c`, `--c0`, `--c1`
      - `--bc`, `--bg`, `--hover`
      - `--b-t22`, `--b-t44`, `--b-t77`, `--b-t88`, `--b-t95`
      - `--bor`
      - `--btn`, `--btn1`, `--btn2`, `--btn3`, `--btn4`
      - `--bt-bx`
      - `--bs`, `--bss`, `--bs-b`, `--bs-bb`, `--bs-bs`, `--bs-bt`, `--bs-bl`, `--bs-br`
      - `--lg`, `--lg1`, `--lg-bl`, `--lg-b`, `--lg-b1`

    Meaning contract:
    - If creating `User/css/0.css` during update, preserve the same variable keys and meanings from `PyApp_Structure-Planning` CSS variable dictionary (only values change per theme).
