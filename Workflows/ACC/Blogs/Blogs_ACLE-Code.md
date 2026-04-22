---
WorkflowId: Blogs_ACLE-Code
Type: Workflow_Singular-Code
Domain: Blogs
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Blogs_ACLE
Purpose: >
  Singular Code workflow: given a single Blog article path, us article ko
  ULTRA Premium cinematic HTML video me convert karo by executing the converter
  script. Yeh workflow actual conversion execute karta hai — Python script
  run karta hai, progress track karta hai, aur results verify karta hai.
Invocation:
  Signature: Workflow("Blogs_ACLE-Code", "Healthline/EN/What_Is_Diabetes", "Convert blog article to ULTRA Premium cinematic video")
  TargetsMeaning: >
    Single article path relative to /Aliens/Blogs/ without .html extension.
    Comma list is invalid (singular workflow).
    Example: "Healthline/EN/What_Is_Diabetes" implies source "/Aliens/Blogs/Healthline/EN/What_Is_Diabetes.html"
    and output "/Aliens/Blogs_ACLE/Healthline/EN/What_Is_Diabetes.html".
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Code:
    Path: "/Aliens/Blogs_ACLE/{ArticlePath}.html"
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
  - "/Aliens/Cyborg/AlienCyborg/Code/convert_blogs_acle.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_blogs_acle.py"
  - "/Aliens/ACLE/assets/css/aliens-cinematic.css"
  - "/Aliens/ACLE/assets/js/aliens-cinematic.js"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - The "code" here is the conversion output (HTML + JSON + audio) not traditional source code.
  - Converter script: `/Aliens/Cyborg/AlienCyborg/Code/convert_blogs_acle.py`
  - Batch orchestrator: `/Aliens/Cyborg/AlienCyborg/Code/batch_blogs_acle.py`
  - Source repo: `/Aliens/Blogs/` (READ-ONLY)
  - Output repo: `/Aliens/Blogs_ACLE/` (WRITE)
  - Source is HTML files (Aliens Blog format from Blogs_New pipeline), not .md files.
  - Converter must parse HTML source, extract article content, generate cinematic slides.
---

# Blogs_ACLE-Code

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
- Source Blogs repo modify karna forbidden
- Converter scripts modify karna forbidden (is workflow me sirf run karo)
- Output sirf `/Aliens/Blogs_ACLE/` me likho

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "Healthline/EN/What_Is_Diabetes")
- Relative to `/Aliens/Blogs/` without `.html` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

# [05] Code Execution Steps

## Step 1: Pre-flight
- Verify source file exists: `/Aliens/Blogs/{ArticlePath}.html`
- Verify output root exists: `/Aliens/Blogs_ACLE/`
- Verify converter script exists: `/Aliens/Cyborg/AlienCyborg/Code/convert_blogs_acle.py`

## Step 2: Run Converter
Execute:
```bash
cd /Aliens/Cyborg/AlienCyborg/Code
python convert_blogs_acle.py --article {ArticlePath}
```

The script will:
- Read source `.html` from `/Aliens/Blogs/{ArticlePath}.html`
- Parse article content (title, sections, text) from Aliens Blog HTML
- Generate cinematic HTML at `/Aliens/Blogs_ACLE/{ArticlePath}.html`
- Generate narrations JSON at `/Aliens/Blogs_ACLE/{ArticlePath_Parent}/{ArticleName}_narrations.json`
- Generate TTS audio at `/Aliens/Blogs_ACLE/{ArticlePath_Parent}/audio_{ArticleName}/`

## Step 3: Verify Output
- HTML file exists and > 5KB
- Narrations JSON exists
- Audio directory exists with 3+ mp3 files
- Branding check: HTML contains "Aliens Blogs" (not "Aliens Wiki")

## Step 4: Report
- Success: output paths + file sizes + slide count
- Failure: error type + stack trace + partial output info
