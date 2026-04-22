---
WorkflowId: Blog_ACLE-Code
Type: Workflow_Singular-Code
Domain: Blog
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Blog_ACLE
Purpose: >
  Singular Code workflow: given a single original Blog article path, us article ko
  ULTRA Premium cinematic HTML video me convert karo by executing the converter
  script. Yeh workflow actual conversion execute karta hai — Python script
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Blog_ACLE-Code", "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura", "Convert blog article to cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Blog/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura" implies
    source "/Aliens/Blog/HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura.md"
    and output "/Aliens/Blog_ACLE/HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura.html".
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Code:
    Path: "/Aliens/Blog_ACLE/{ArticlePath}.html"
    Pattern: "*.html + *_narrations.json + audio_*/ (per article)"
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
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_blog_acle.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_blog_acle.py"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - Converter script: convert_blog_acle.py (wraps convert_article.py engine for Blog paths + branding).
  - Source Blog repo is READ-ONLY. Output goes to Blog_ACLE.
  - Internet required for edge-tts (Microsoft Azure TTS API).
  - Conversion is idempotent: already-converted articles skipped.
  - Branding: "Aliens Blog" (not "Aliens Wiki").
---

# Blog_ACLE-Code

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
- Sirf specified article ko convert karo
- Source Blog repo (`/Aliens/Blog/`) me koi modification forbidden
- Converter scripts modify karna forbidden
- Shared assets (`/Aliens/ACLE/assets/`) modify karna forbidden
- Koi extra article add karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura")
- Relative to `/Aliens/Blog/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"
- Code output write permission (writes to Blog_ACLE, not prod code — generally allowed).
- Approval policy: manual => approval before starting conversion.

# [06] Plan-Driven Execution (Optional)
Plan file check:
- Path: `/Aliens/.Alien/{Developer_Username}/Planning/Blog_ACLE/{ArticleSlug}.plan.md`

Rules:
- If plan exists: read plan, use discovery data.
- If plan missing: conversion may still proceed (SpeedMode bypasses planning), log warning.

# [07] Environment Validation (Pre-Flight)
## 7.1 Python Environment
- `python --version` >= 3.10

## 7.2 edge-tts Available
- `python -c "import edge_tts"` — must succeed (unless --no-audio).

## 7.3 Source Article Exists
- `/Aliens/Blog/{ArticlePath}.md` — must exist and be non-empty.

## 7.4 Output Directory
- `/Aliens/Blog_ACLE/` — must exist (at least repo root with LICENSE/README).

# [08] Execution (Single Article Conversion)
Run command:
```
cd /Aliens/Cyborg/AlienCyborg/Code
python convert_blog_acle.py --article {ArticlePath}
```

Where `{ArticlePath}` = Targets value (e.g. "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura").

# [09] Output Verification (Post-Conversion)
After conversion, verify:

## 9.1 HTML Output
- Path: `/Aliens/Blog_ACLE/{ArticlePath}.html`
- Exists? Size > 5KB?
- Contains "Aliens Blog" branding (not "Aliens Wiki")?

## 9.2 Narrations JSON
- Path: `/Aliens/Blog_ACLE/{Language}/audio_{ArticleName}/{ArticleName}_narrations.json`
  OR: `/Aliens/Blog_ACLE/{ArticlePath.parent}/{ArticleName}_narrations.json`
- Exists? Valid JSON?

## 9.3 Audio Files
- Path: `/Aliens/Blog_ACLE/{ArticlePath.parent}/audio_{ArticleName}/`
- Directory exists? Contains >= 3 mp3 files?

# [10] Acceptance Criteria
Code success tabhi:
- HTML output exists and > 5KB
- Narrations JSON exists and valid
- Audio directory exists with 3+ mp3 files
- "Aliens Blog" branding present in HTML (not "Aliens Wiki")
