---
WorkflowId: Blogs_ACLE-Documentation
Type: Workflow_Singular-Documentation
Domain: Blogs
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Blogs_ACLE
Purpose: >
  Singular Documentation workflow: given a single Blog article path, uski ACLE conversion ka
  enterprise-grade documentation generate karo — covering article overview, conversion results,
  quality metrics, troubleshooting, aur usage guide. Documentation file
  Docs/Blogs_ACLE/ me save hoti hai.
Invocation:
  Signature: Workflow("Blogs_ACLE-Documentation", "Healthline/EN/What_Is_Diabetes", "Document the ACLE Blogs conversion results")
  TargetsMeaning: >
    Single article path relative to /Aliens/Blogs/ without .html extension.
    Comma list is invalid (singular workflow).
    Example: "Healthline/EN/What_Is_Diabetes" implies conversion results at "/Aliens/Blogs_ACLE/Healthline/EN/What_Is_Diabetes.html".
    Note: Range filtering is handled by the parent Plural workflow via Description.
    This singular workflow always receives a single resolved article path.
  DescriptionMeaning: >
    Human language constraints/focus. Must not expand scope. If empty/missing -> fail fast.
    May contain range suffix from parent but this workflow ignores it
    (range already applied by Plural before calling this child).
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Blogs_ACLE/"
    Pattern: "{ArticleSlug}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
  - Workflows/ACC/_Refs/ACC.Targets.md
  - Workflows/ACC/_Refs/ACC.Paths.md
  - Workflows/ACC/_Refs/ACC.Output.md
Notes:
  - Documentation is generated AFTER conversion (Code workflow) completes.
  - Documentation reflects actual conversion results from disk (not guessed).
  - Source Blogs repo is READ-ONLY.
  - This workflow does NOT run any conversion. It only documents what happened.
  - Documentation language follows ACC SSOT: Hinglish (Roman Hindi).
  - English sirf technical terms ke liye. Devanagari Hindi script allowed nahi.
  - ArticleSlug = ArticlePath with `/` replaced by `_` (e.g. Healthline/EN/What_Is_Diabetes → Healthline_EN_What_Is_Diabetes).
---

# Blogs_ACLE-Documentation

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"

# [02] References (MANDATORY)
require_once "Workflows/ACC/_Refs/ACC.Manifest.md"
require_once "Workflows/ACC/_Refs/ACC.Safety.md"
require_once "Workflows/ACC/_Refs/ACC.Targets.md"
require_once "Workflows/ACC/_Refs/ACC.Paths.md"
require_once "Workflows/ACC/_Refs/ACC.Output.md"

# [03] Strict Scope Lock
- Sirf specified article ki documentation banao
- Extra articles add karna forbidden
- Source Blogs repo modify karna forbidden
- Conversion output files modify karna forbidden
- Converter scripts modify karna forbidden
- Is workflow me code execute karna forbidden (sirf documentation karo)

# [04] Inputs
Targets:
- MUST be exactly ONE article path (example: "Healthline/EN/What_Is_Diabetes")
- Relative to `/Aliens/Blogs/` without `.html` extension
- Path separators: both `/` and `\` accepted, normalize to `/`
- Comma-separated list => error (`WF_TARGETS_INVALID`)

# [05] Documentation Steps

## Step 1: Gather Conversion Results
Read from disk:
- Source: `/Aliens/Blogs/{ArticlePath}.html` (word count, section count)
- Output HTML: `/Aliens/Blogs_ACLE/{ArticlePath}.html` (size, slide count)
- Output Audio: `/Aliens/Blogs_ACLE/{Parent}/audio_{ArticleName}/` (mp3 count)
- Narrations: `/Aliens/Blogs_ACLE/{Parent}/{ArticleName}_narrations.json`

## Step 2: Quality Metrics
Determine from actual file data:
- HTML size (bytes)
- Slide count (parse HTML for slide markers)
- Audio file count
- Total audio duration (if available from narrations JSON)
- Branding correct: "Aliens Blogs" present

## Step 3: Generate Documentation
Save to: `/Aliens/Docs/Blogs_ACLE/{ArticleSlug}.md`

Documentation format:
```markdown
# Blogs_ACLE — {ArticleName}

## Overview
- Source: /Aliens/Blogs/{ArticlePath}.html
- Output: /Aliens/Blogs_ACLE/{ArticlePath}.html
- Converted by: convert_blogs_acle.py v1.0.0
- Blog: {BlogName}

## Source Analysis
- Word count: {N}
- Sections: {N}
- Language: {EN/HIEN/HI}
- Key topics: {list}

## Conversion Results
- HTML: {size} bytes, {slides} slides
- Audio: {count} mp3 files
- Narrations: {count} entries

## Quality Check
- HTML valid: {yes/no}
- Audio complete: {yes/no}
- Branding correct: {yes/no}
- Content coverage: {full/partial}

## Troubleshooting
{any issues encountered}
```
