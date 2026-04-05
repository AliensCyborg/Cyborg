---
WorkflowId: Blogs_New-Code
Type: Workflow_Singular-Code
Domain: Blogs
Category: Blog Article Scraping + Translation Pipeline
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Blogs_New
Purpose: >
  Singular Code workflow: given a single blog article URL, us article ko fetch karo,
  content extract karo, Aliens HTML format me refactor karke EN save karo,
  phir Hinglish (HIEN) aur Hindi (HI) me translate karke save karo.
Invocation:
  Signature: Workflow("Blogs_New-Code", "https://healthline.com/health/diabetes", "Fetch, refactor, and translate blog article")
  TargetsMeaning: >
    Single article URL.
    Example: "https://healthline.com/health/diabetes"
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Code:
    EN_Path: "/Aliens/Blogs/{BlogName}/EN/{Article_Slug}.html"
    HIEN_Path: "/Aliens/Blogs/{BlogName}/HIEN/{Article_Slug}.html"
    HI_Path: "/Aliens/Blogs/{BlogName}/HI/{Article_Slug}.html"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Code.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
ExampleFiles:
  - "/Aliens/Cyborg/AlienCyborg/Code/blogs_new.py"
  - "/Aliens/Cyborg/AlienCyborg/Code/batch_blogs.py"
Notes:
  - This workflow RUNS Python scripts — it does NOT write code files directly.
  - Internet required for fetching articles and LLM translation.
  - OPENAI_API_KEY env var required for translation.
  - Script: `/Aliens/Cyborg/AlienCyborg/Code/blogs_new.py --article {url}`
---

# Blogs_New-Code

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Code.md"

# [02] Strict Scope Lock
- Sirf specified article process karo
- Extra articles process karna forbidden
- Output sirf `/Aliens/Blogs/{BlogName}/` me likho

# [03] Code Execution Steps

## Step 1: Pre-flight
- Verify internet connection
- Verify OPENAI_API_KEY set (if translation needed)
- Verify output root exists or create: `/Aliens/Blogs/{BlogName}/`

## Step 2: Fetch Article
- HTTP GET article URL
- Verify response 200
- Extract raw HTML

## Step 3: Extract Content
- Remove scripts, styles, navigation, ads, sidebars
- Find main article content (<article>, <main>, or largest content div)
- Extract title from <h1> or <title>
- Clean body HTML: keep h1-h6, p, ul, ol, li, table, blockquote, img, a, strong, em, code, pre
- Make image URLs absolute

## Step 4: Refactor to Aliens HTML Format (EN)
- Wrap extracted content in Aliens Blog HTML template
- Include: meta tags, inline CSS, brand header, article body, footer
- Save to: `/Aliens/Blogs/{BlogName}/EN/{Article_Slug}.html`

## Step 5: Translate to Hinglish (HIEN)
- Send body HTML sections to LLM with Hinglish translation prompt
- Preserve all HTML tags, only translate text
- Wrap translated content in same template (lang="hi-Latn")
- Save to: `/Aliens/Blogs/{BlogName}/HIEN/{Article_Slug}.html`

## Step 6: Translate to Hindi (HI)
- Send body HTML sections to LLM with Hindi translation prompt
- Preserve all HTML tags, only translate text
- Wrap translated content in same template (lang="hi")
- Save to: `/Aliens/Blogs/{BlogName}/HI/{Article_Slug}.html`

## Step 7: Verify Output
- All 3 files exist
- Files are non-empty (> 1KB)
- HTML is well-formed

## Step 8: Report
- Success: output paths + file sizes
- Failure: error type + partial output info
