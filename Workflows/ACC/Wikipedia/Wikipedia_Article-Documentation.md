---
WorkflowId: Wikipedia_Article-Documentation
Type: Workflow_Singular
Domain: Wikipedia
Category: Wikipedia Article Documentation
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
Purpose: >
  Single Wikipedia article ke liye documentation artifact generate karo.
  SpeedMode me yeh workflow BYPASSED hota hai — sirf reference ke liye exist karta hai.
Parent: Wikipedia_Article
SpeedMode_Bypass: true
---

# Wikipedia_Article-Documentation

# [00] SpeedMode Bypass Notice
```
⚡ SPEED MODE: Yeh workflow default bypass hai.
Wikipedia_Article parent workflow me SpeedMode.Enabled = true hai.
Documentation step skip hota hai for maximum throughput.
Agar documentation chahiye, to SpeedMode off karke explicitly call karo.
```

# [01] Documentation Steps (When NOT Bypassed)
Agar SpeedMode off ho aur yeh workflow explicitly call ho:

## Step 1: Gather Article Info
- Article title, slug, letter, folder
- EN file path + size
- HIEN file path + size
- HI file path + size
- Download timestamp
- Translation timestamps

## Step 2: Quality Assessment
- EN markup count ({{, [[, ==, <ref>)
- HIEN markup preservation rate
- HI markup preservation rate
- File size ratios

## Step 3: Generate Documentation
- Output: `/Aliens/Docs/Wikipedia_Article/{Slug}.md`
- Contains:
  - Article metadata
  - File locations (EN, HIEN, HI)
  - Translation quality report
  - Markup preservation stats
  - Processing timestamps

# [02] Output
```
/Aliens/Docs/Wikipedia_Article/{Slug}.md
```
