---
WorkflowId: Blogs_New-Documentation
Type: Workflow_Singular-Documentation
Domain: Blogs
Category: Blog Article Scraping + Translation Pipeline
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Blogs_New
Purpose: >
  Singular Documentation workflow: given a blog domain, uski processing pipeline ka
  enterprise-grade documentation generate karo — covering blog overview, sitemap stats,
  article processing results, translation quality, troubleshooting, aur usage guide.
Invocation:
  Signature: Workflow("Blogs_New-Documentation", "healthline.com", "Document the Blogs_New processing results")
  TargetsMeaning: >
    Blog domain name (e.g. "healthline.com").
  DescriptionMeaning: >
    Human language constraints/focus. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Blogs_New/"
    Pattern: "{BlogSlug}.md"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Documentation.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
Notes:
  - Documentation is generated AFTER article processing completes.
  - Documentation reflects actual results from disk and CSV (not guessed).
  - This workflow does NOT process any articles. It only documents what happened.
  - Documentation language: Hinglish (Roman Hindi).
---

# Blogs_New-Documentation

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Documentation.md"

# [02] Strict Scope Lock
- Sirf specified blog ki documentation banao
- Articles process karna forbidden
- Converter scripts modify karna forbidden

# [03] Documentation Steps

## Step 1: Gather Blog Stats
- Read planner CSV: `/Aliens/Project/{CyborgID}/{BlogName}.csv`
- Count: total articles, pending, success, failed
- Read output directory: `/Aliens/Blogs/{BlogName}/`
- Count: EN files, HIEN files, HI files

## Step 2: Generate Documentation
Save to: `/Aliens/Docs/Blogs_New/{BlogSlug}.md`

```markdown
# Blogs_New — {BlogName}

## Overview
- Blog: {domain}
- BlogName: {BlogName}
- Planner CSV: /Aliens/Project/{CyborgID}/{BlogName}.csv

## Sitemap Stats
- Total articles discovered: {N}
- Articles processed: {N}
- Articles pending: {N}
- Articles failed: {N}

## Output Stats
- EN files: {N}
- HIEN files: {N}
- HI files: {N}
- Total size: {size}

## Processing Results
{summary of processed articles}

## Troubleshooting
{any issues found}
```
