---
WorkflowId: Wikipedia_Article-Planning
Type: Workflow_Singular
Domain: Wikipedia
Category: Wikipedia Article Planning
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
Purpose: >
  Single Wikipedia article ke liye planning artifact generate karo.
  SpeedMode me yeh workflow BYPASSED hota hai — sirf reference ke liye exist karta hai.
Parent: Wikipedia_Article
SpeedMode_Bypass: true
---

# Wikipedia_Article-Planning

# [00] SpeedMode Bypass Notice
```
⚡ SPEED MODE: Yeh workflow default bypass hai.
Wikipedia_Article parent workflow me SpeedMode.Enabled = true hai.
Planning step skip hota hai for maximum throughput.
Agar detail planning chahiye, to SpeedMode off karke explicitly call karo.
```

# [01] Planning Steps (When NOT Bypassed)
Agar SpeedMode off ho aur yeh workflow explicitly call ho:

## Step 1: Article Existence Check
- Wikipedia API call: verify article exists
- URL: `https://en.wikipedia.org/w/api.php?action=query&titles={Title}&format=json`
- If article missing/redirect → note in plan

## Step 2: Article Size Estimation
- Fetch raw article, measure size
- If > 500KB → flag as "large article, section-by-section required"
- If > 2MB → flag as "very large, may need batched translation"

## Step 3: Translation Feasibility
- Check OPENAI_API_KEY available
- Estimate token count (rough: chars / 4)
- Estimate cost (gpt-4o-mini: ~$0.15/1M input tokens)

## Step 4: Folder Assignment
- Determine target folder for each language (EN/HIEN/HI)
- Check folder file count, assign {Letter}{Seq} folder

## Step 5: Generate Plan File
- Output: `/Aliens/.Alien/{Developer_Username}/Planning/Wikipedia_Article/{Slug}.plan.md`
- Contains: article info, size, folder assignments, estimated cost, feasibility

# [02] Output
```
/Aliens/.Alien/{Developer_Username}/Planning/Wikipedia_Article/{Slug}.plan.md
```
