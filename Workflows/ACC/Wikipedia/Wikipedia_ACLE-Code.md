---
WorkflowId: Wikipedia_ACLE-Code
Type: Workflow_Singular-Code
Domain: Wikipedia
Category: Wikipedia Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Wikipedia_ACLE
Purpose: >
  Singular Code workflow: given a single Wikipedia article path, us article ko
  ULTRA Premium cinematic HTML video me convert karo by executing the converter
  script. Yeh workflow actual conversion execute karta hai — Python script
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Wikipedia_ACLE-Code", "HIEN/A0001/Albert_Einstein", "Convert Wikipedia article to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Wikipedia/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/A0001/Albert_Einstein" implies source "/Aliens/Wikipedia/HIEN/A0001/Albert_Einstein.md"
    and output "/Aliens/Wikipedia_ACLE/HIEN/A0001/Albert_Einstein.html".
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Code:
    Path: "/Aliens/Wikipedia_ACLE/{ArticlePath}.html"
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
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_wikipedia.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_wikipedia_acle.py"
  - "/Aliens/ACLE/assets/css/aliens-cinematic.css"
  - "/Aliens/ACLE/assets/js/aliens-cinematic.js"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - The "code" here is the conversion output (HTML + JSON + audio) not traditional source code.
  - Converter script: `/Aliens/Cyborg/AlienCyborg/Code/convert_wikipedia.py`
  - Batch orchestrator: `/Aliens/Cyborg/AlienCyborg/Code/batch_wikipedia_acle.py`
  - Source repo: `/Aliens/Wikipedia/` (READ-ONLY)
  - Output repo: `/Aliens/Wikipedia_ACLE/` (WRITE)
---

# Wikipedia_ACLE-Code

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
- Sirf specified article convert karo
- Extra articles convert karna forbidden
- Source Wikipedia repo modify karna forbidden
- Converter scripts modify karna forbidden (is workflow me sirf run karo)
- Output sirf `/Aliens/Wikipedia_ACLE/` me likho

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "HIEN/A0001/Albert_Einstein")
- Relative to `/Aliens/Wikipedia/` without `.md` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

# [05] Code Execution Steps

## Step 1: Pre-flight
- Verify source file exists: `/Aliens/Wikipedia/{ArticlePath}.md`
- Verify output root exists: `/Aliens/Wikipedia_ACLE/`
- Verify converter script exists: `/Aliens/Cyborg/AlienCyborg/Code/convert_wikipedia.py`

## Step 2: Run Converter
Execute:
```bash
cd /Aliens/Cyborg/AlienCyborg/Code
python convert_wikipedia.py --article {ArticlePath}
```

The script will:
- Read source `.md` from `/Aliens/Wikipedia/{ArticlePath}.md`
- Generate cinematic HTML at `/Aliens/Wikipedia_ACLE/{ArticlePath}.html`
- Generate narrations JSON at `/Aliens/Wikipedia_ACLE/{ArticlePath_Parent}/{ArticleName}_narrations.json`
- Generate TTS audio at `/Aliens/Wikipedia_ACLE/{ArticlePath_Parent}/audio_{ArticleName}/`

## Step 3: Verify Output
- HTML file exists and > 5KB
- Narrations JSON exists
- Audio directory exists with 3+ mp3 files
- Branding check: HTML contains "Aliens Wikipedia" (not "Aliens Wiki")

## Step 4: Report
- Success: output paths + file sizes + slide count
- Failure: error type + stack trace + partial output info
