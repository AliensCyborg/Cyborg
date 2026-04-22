---
WorkflowId: Role_ACLE-Code
Type: Workflow_Singular-Code
Domain: Role
Category: Role SOP Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Role_ACLE
Purpose: >
  Singular Code workflow: given a single Role SOP document path, us document ko
  ULTRA Premium cinematic HTML video me convert karo by executing the converter
  script. Yeh workflow actual conversion execute karta hai — Python script
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Role_ACLE-Code", "Software_Engineer", "Convert role SOP to cinematic video")
  TargetsMeaning: >
    Single role filename stem from /Aliens/Role/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "Software_Engineer" implies
    source "/Aliens/Role/Software_Engineer.md"
    and output "/Aliens/Role_ACLE/Software_Engineer.html".
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Code:
    Path: "/Aliens/Role_ACLE/{RoleSlug}.html"
    Pattern: "*.html + *_narrations.json + audio_*/ (per role)"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
ExampleFiles:
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_role_acle.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_role_acle.py"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - Converter script: convert_role_acle.py (wraps convert_article.py engine for Role paths + branding).
  - Source Role repo is READ-ONLY. Output goes to Role_ACLE.
  - Internet required for edge-tts (Microsoft Azure TTS API).
  - Conversion is idempotent: already-converted roles skipped.
  - Branding: "Aliens Role" (not "Aliens Wiki" or "Aliens Blog").
  - Role SOP files are large (300-1800+ lines) — conversion may produce many slides.
---

# Role_ACLE-Code

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Code.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/ACC.Paths.md"
require_once "Workflows/ACC/_Refs/ACC.Output.md"

# [03] Strict Scope Lock
- Sirf specified role ko convert karo
- Source Role repo (`/Aliens/Role/`) me koi modification forbidden
- Converter scripts modify karna forbidden
- Shared assets (`/Aliens/ACLE/assets/`) modify karna forbidden
- Koi extra role add karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE role filename stem (example: "Software_Engineer")
- Filename stem from `/Aliens/Role/` without `.md` extension
- Comma-separated list => error (`WF_TARGETS_INVALID`)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"
- Code output write permission (writes to Role_ACLE, not prod code — generally allowed).
- Approval policy: manual => approval before starting conversion.

# [06] Plan-Driven Execution (Optional)
Plan file check:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Role_ACLE/{RoleSlug}.plan.md`

Rules:
- If plan exists: read plan, use discovery data.
- If plan missing: conversion may still proceed (SpeedMode bypasses planning), log warning.

# [07] Environment Validation (Pre-Flight)
## 7.1 Python Environment
- `python --version` >= 3.10

## 7.2 edge-tts Available
- `python -c "import edge_tts"` — must succeed (unless --no-audio).

## 7.3 Source Document Exists
- `/Aliens/Role/{RoleSlug}.md` — must exist and be non-empty.

## 7.4 Output Directory
- `/Aliens/Role_ACLE/` — must exist (at least repo root with LICENSE/README).

# [08] Execution (Single Role Conversion)
Run command:
```
cd /Aliens/Cyborg/AlienCyborg/Code
python convert_role_acle.py --article {RoleSlug}
```

Where `{RoleSlug}` = Targets value (e.g. "Software_Engineer").

# [09] Output Verification (Post-Conversion)
After conversion, verify:

## 9.1 HTML Output
- Path: `/Aliens/Role_ACLE/{RoleSlug}.html`
- Exists? Size > 5KB?
- Contains "Aliens Role" branding (not "Aliens Wiki")?

## 9.2 Narrations JSON
- Path: `/Aliens/Role_ACLE/{RoleSlug}_narrations.json`
  OR: `/Aliens/Role_ACLE/audio_{RoleSlug}/{RoleSlug}_narrations.json`
- Exists? Valid JSON?

## 9.3 Audio Files
- Path: `/Aliens/Role_ACLE/audio_{RoleSlug}/`
- Directory exists? Contains >= 3 mp3 files?

# [10] Acceptance Criteria
Code success tabhi:
- HTML output exists and > 5KB
- Narrations JSON exists and valid
- Audio directory exists with 3+ mp3 files
- "Aliens Role" branding present in HTML (not "Aliens Wiki")
