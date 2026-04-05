---
WorkflowId: Wikipedia_Article-Code
Type: Workflow_Singular
Domain: Wikipedia
Category: Wikipedia Article Download + Translation Execution
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-14
Purpose: >
  Single Wikipedia article ka full pipeline execute karo:
  Download raw wikitext (EN) → Translate to Hinglish (HIEN) → Translate to Hindi (HI) → Save all.
  Yeh primary execution workflow hai — SpeedMode me sirf yahi run hota hai.
Parent: Wikipedia_Article
---

# Wikipedia_Article-Code

# [01] Environment Validation
Required:
- Python 3.10+
- Internet connectivity (Wikipedia API + OpenAI API)
- OPENAI_API_KEY environment variable SET (for translation)
- Scripts at `/Aliens/Cyborg/AlienCyborg/Code/`:
  - `wikipedia_article.py` (core module)
  - `batch_wikipedia.py` (batch orchestrator)
- Write access to `/Aliens/Wikipedia/` (EN, HIEN, HI directories)

Validation:
```
1) python --version >= 3.10
2) OPENAI_API_KEY is set and non-empty
3) wikipedia_article.py exists at Code/ path
4) /Aliens/Wikipedia/EN/ directory exists
5) /Aliens/Wikipedia/HIEN/ directory exists
6) /Aliens/Wikipedia/HI/ directory exists (create if needed)
7) Internet connectivity test (ping en.wikipedia.org)
```

Missing => STOP with `WF_ENV_INVALID`

# [02] Execution Steps

## Step 1: Resolve Article
- Input: article title/slug (from parent workflow or CSV row `name` column)
- Normalize: spaces → underscores, trim whitespace
- Wikipedia raw URL: `https://en.wikipedia.org/w/index.php?title={Title}&action=raw`

## Step 2: Check if Already Done
- Check EN file exists: `/Aliens/Wikipedia/EN/{folder}/{slug}.md`
- Check HIEN file exists: `/Aliens/Wikipedia/HIEN/{folder}/{slug}.md`
- Check HI file exists: `/Aliens/Wikipedia/HI/{folder}/{slug}.md`
- If all 3 exist and non-empty → SKIP (already processed)

## Step 3: Download English Raw Article
- HTTP GET: `https://en.wikipedia.org/w/index.php?title={Title}&action=raw`
- Retry: 3 attempts with exponential backoff (1s, 2s, 4s)
- Validate: response не empty, status 200
- If 404 / missing → mark as failed, skip
- Save to: `/Aliens/Wikipedia/EN/{folder}/{slug}.md`
- Folder determined by `get_target_folder("EN", letter)`

## Step 4: Translate to Hinglish (HIEN)
- Input: raw wikitext from Step 3
- Method: Section-by-section LLM translation
  1) Split by `\n== ` (level-2 headings)
  2) Lead section (before first ==) translated separately
  3) Each section → LLM call with Hinglish translation prompt
  4) Reassemble all sections
- Retry per section: 3 attempts
- Save to: `/Aliens/Wikipedia/HIEN/{folder}/{slug}.md`
  (SAME folder name as EN for cross-language consistency)
- If translation fails completely → save partial with [TRANSLATION_FAILED] markers

## Step 5: Translate to Hindi (HI)
- Same approach as Step 4 but target = Hindi (Devanagari)
- Save to: `/Aliens/Wikipedia/HI/{folder}/{slug}.md`
- Same folder name as EN

## Step 6: Verify Outputs
- EN file: exists, size > 0
- HIEN file: exists, size > 0, markup preserved (spot check: `{{`, `[[`, `==` present)
- HI file: exists, size > 0, contains Devanagari characters

## Step 7: Update Progress
- If CSV mode: update row (en_saved, hien_saved, hi_saved, status, modified)
- If single mode: print success summary

# [03] Translation Prompt Templates

## Hinglish Prompt:
```
You are a Wikipedia article translator. Translate the following Wikipedia
wikitext section to Hinglish (Hindi written in Roman/Latin script, naturally
mixed with English words).

CRITICAL RULES:
1. Preserve ALL wikitext markup EXACTLY: {{templates}}, [[links]], [[File:...]],
   <ref>...</ref>, <!-- comments -->, {| tables |}, categories, infoboxes
2. Only translate readable prose/text content
3. Keep proper nouns in English (Albert Einstein, Germany, Nobel Prize, etc.)
4. Keep technical terms in English (algorithm, photon, relativity, etc.)
5. Keep filenames, URLs, image names, parameter names UNCHANGED
6. Keep numbers, dates, math expressions AS-IS
7. Write natural conversational Hinglish (jaise educated Indians daily bolte hain)
8. Output structure must EXACTLY match input (same lines, same formatting)
9. Do NOT add explanations or notes — output ONLY the translated wikitext

Section to translate:
```

## Hindi Prompt:
```
You are a Wikipedia article translator. Translate the following Wikipedia
wikitext section to Hindi (Devanagari script).

CRITICAL RULES:
1. Preserve ALL wikitext markup EXACTLY: {{templates}}, [[links]], [[File:...]],
   <ref>...</ref>, <!-- comments -->, {| tables |}, categories, infoboxes
2. Only translate readable prose/text content to Hindi (Devanagari)
3. Keep proper nouns in English within parentheses where helpful
4. Technical terms: Hindi first, English in parentheses — e.g. सापेक्षता (Relativity)
5. Keep filenames, URLs, image names, parameter names UNCHANGED
6. Keep numbers, dates, math expressions AS-IS
7. Write formal encyclopedic Hindi (Wikipedia standard)
8. Output structure must EXACTLY match input (same lines, same formatting)
9. Do NOT add explanations or notes — output ONLY the translated wikitext

Section to translate:
```

# [04] Quality Gates
- Markup integrity: translated output me `{{`, `[[`, `]]`, `}}`, `<ref>`, `==` count
  input ke barabar ya close hona chahiye (±10% tolerance for nested templates)
- File size: translated file ka size original ke 50%-200% range me hona chahiye
- Encoding: UTF-8, no BOM
- No [TRANSLATION_FAILED] markers in final output (ideally)

# [05] CLI Execution Reference
```bash
# Single article
python wikipedia_article.py --title Albert_Einstein

# Download only (skip translation)
python wikipedia_article.py --title Albert_Einstein --download-only

# Translate existing EN file (skip download)
python wikipedia_article.py --title Albert_Einstein --translate-only

# Skip Hindi (only EN + HIEN)
python wikipedia_article.py --title Albert_Einstein --no-hi

# Batch: process next pending from CSV
python batch_wikipedia.py --run A

# Batch: build CSV for letter A
python batch_wikipedia.py --build-csv A
```

# [06] Error Recovery
- Download 404: article doesn't exist on Wikipedia → mark failed, skip
- Download timeout: retry 3x → mark failed
- Translation API error: retry 3x per section → partial save with markers
- API key missing: fail-fast with WF_API_KEY_MISSING
- Folder full (1000): auto-create next folder (handled by script)
- Duplicate article: check before download, skip if all 3 files exist
