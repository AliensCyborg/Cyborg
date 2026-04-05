---
WorkflowId: Blogs_New-Planning
Type: Workflow_Singular-Planning
Domain: Blogs
Category: Blog Article Scraping + Translation Pipeline
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
EntryPoint: false
ParentWorkflow: Blogs_New
Purpose: >
  Singular Planning workflow: given a blog domain, us blog ka sitemap extract karo,
  article URLs discover karo, aur Planner CSV banao jo har article ka record rakhega.
  Agar CSV already exists, to status report do. Is workflow me articles process nahi hote —
  sirf discovery + planning hoti hai.
Invocation:
  Signature: Workflow("Blogs_New-Planning", "healthline.com", "Extract sitemap and create article planner")
  TargetsMeaning: >
    Blog domain name (e.g. "healthline.com").
    For CSV mode, this step is skipped (CSV already exists).
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Planning:
    Path: "/Aliens/Project/{Developer_Username}/{BlogName}.csv"
    Pattern: "{BlogName}.csv"
Requires:
  - Workflows/ACC/_Common/Workflow_Universal.md
  - Workflows/ACC/_Common/Workflow_Singular.md
  - Workflows/ACC/_Common/Workflow_Singular-Planning.md
  - Workflows/ACC/_Refs/ACC.Manifest.md
  - Workflows/ACC/_Refs/ACC.Safety.md
Notes:
  - This workflow does NOT process articles. It only creates the planner CSV.
  - Sitemap extraction requires internet connection.
  - Output: Planner CSV at `/Aliens/Project/{CyborgID}/{BlogName}.csv`
  - Script: `/Aliens/Cyborg/AlienCyborg/Code/blogs_new.py --sitemap {domain}`
---

# Blogs_New-Planning

# [00] Universal Include (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Universal.md"

# [01] Type Includes (MANDATORY)
require_once "Workflows/ACC/_Common/Workflow_Singular.md"
require_once "Workflows/ACC/_Common/Workflow_Singular-Planning.md"

# [02] Strict Scope Lock
- Sirf sitemap extraction + CSV creation karo
- Articles fetch/process karna forbidden (Code workflow karega)
- Source blog ko modify karna obviously not applicable (external website)
- Is workflow me translation karna forbidden

# [03] Planning Steps

## Step 1: Validate Domain
- Domain format check: at least one dot, no spaces, valid characters
- Derive BlogName: domain ka first segment, first letter capitalize

## Step 2: Check Existing CSV
- Check if `/Aliens/Project/{CyborgID}/{BlogName}.csv` already exists
- If exists and has rows → report status, optionally refresh/append

## Step 3: Extract Sitemap
Execute:
```bash
cd /Aliens/Cyborg/AlienCyborg/Code
python blogs_new.py --sitemap {domain}
```

Process:
1. Try `https://{domain}/sitemap.xml`
2. Fallback: `https://{domain}/sitemap_index.xml`
3. Fallback: `https://{domain}/robots.txt` → parse `Sitemap:` lines
4. Fallback: `https://{domain}/wp-sitemap.xml`
5. Parse XML → extract `<loc>` URLs
6. Handle sitemap index → recursively fetch child sitemaps
7. Filter article URLs (skip category/tag/author pages)
8. Generate slugs from URLs

## Step 4: Build Planner CSV
- Save to: `/Aliens/Project/{CyborgID}/{BlogName}.csv`
- One row per article URL
- All rows start with status=0 (pending)
- Columns: id, url, slug, title, status, description, en_saved, hien_saved, hi_saved, folder, modified, created, error
