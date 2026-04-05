---
WorkflowId: Blog_Article-Code
WorkflowName: Aliens Blog Article Writer (Multilingual)
Type: Workflow_Singular-Code
ParentWorkflowId: Blog_Article
Domain: Blog
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
Inputs:
  - Targets: "Article_Slug (single only)"
  - Description: "REQUIRED; topic context + requirements"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md"
Produces:
  - EN_Article: "/Aliens/Blog/EN/{Article_Slug}.md"
  - HIEN_Article: "/Aliens/Blog/HIEN/{Article_Slug}.md"
  - HI_Article: "/Aliens/Blog/HI/{Article_Slug}.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/code.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Code.md"
  Refs:
    - "Workflows/ACC/_Refs/ACC.Targets.md"
    - "Workflows/ACC/_Refs/ACC.Paths.md"
    - "Workflows/ACC/_Refs/ACC.Output.md"
    - "Workflows/ACC/_Refs/ACC.Safety.md"
    - "Workflows/ACC/_Refs/ACC.Manifest.md"
    - "Workflows/ACC/_Refs/ACC.Errors.md"
NonNegotiables:
  - NoDelete: true
  - DescriptionRequired: true
  - OneArticleOnly: true
  - BlogStyle: true
  - NoFactInvention: true
  - InternetRequired: false
  - LanguageOrderStrict: "EN → HIEN → HI"
  - PlanningDependency: true
---

# Blog_Article-Code.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Code.md`

---

## [01] Purpose (Hinglish)
Is workflow ka kaam: ek single blog article ko **teeno languages me likhna** (EN → HIEN → HI).
Agent (AlienCyborg) khud article likhta hai — koi script, koi API, koi scraping nahi.
Planning doc ko follow karke enterprise-grade, engaging, deep blog content create karna.

---

## [02] Inputs Rules (Strict)

### Targets
- Single article slug only.
- Multiple targets (comma) => `ACC_ERR_ARGS_INVALID`.

### Slug
- `Article_Slug` = Targets trimmed.
- Already PascalCase_With_Underscores format.

### Title
- If `Description` contains `Title: <value>`: use that as `ArticleTitle`.
- Else: derive from slug (underscores → spaces).

### Description
- Required. Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [03] Planning Dependency (Mandatory)
- Planning file read karo:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md`

If missing:
- Fail fast with `ACC_ERR_PLANNING_GENERATION_FAILED`.

Planning doc ko SSOT treat karo for:
- Section outline
- Key points
- Tone + audience decisions
- Code example plans
- Multilingual adaptation notes

---

## [04] Output Paths (SSOT — Deterministic)
- EN: `/Aliens/Blog/EN/{Article_Slug}.md`
- HIEN: `/Aliens/Blog/HIEN/{Article_Slug}.md`
- HI: `/Aliens/Blog/HI/{Article_Slug}.md`

All 3 files same slug, different content language.

---

## [05] Language Execution Order (NON-NEGOTIABLE)
### Step 1: EN (English)
- Write complete English article first.
- Save to disk at `/Aliens/Blog/EN/{Article_Slug}.md`.
- Verify file exists and has substantial content (not just headers).
- EN is the PRIMARY source — HIEN and HI are derived from it.

### Step 2: HIEN (Hinglish Roman)
- ONLY start after EN file is saved to disk and verified.
- Read the EN article from disk.
- Translate + adapt to Hinglish Roman style.
- HIEN = romanized Hindi-English mix (no Devanagari script at all).
- Maintain same structure (headings, sections, code blocks) as EN.
- Adapt tone: more casual, more desi humor, relatable examples.
- Technical terms stay English. Non-technical terms become Hinglish.
- Save to disk at `/Aliens/Blog/HIEN/{Article_Slug}.md`.

### Step 3: HI (Hindi Devanagari)
- ONLY start after HIEN file is saved to disk and verified.
- Read the EN article from disk (use EN as source, not HIEN).
- Translate + adapt to pure Hindi Devanagari script.
- English technical terms allowed where no Hindi equivalent exists.
- Same structure preserved (headings, sections, code blocks).
- Save to disk at `/Aliens/Blog/HI/{Article_Slug}.md`.

---

## [06] Blog Article Markdown Format (Required)
Every article (all 3 languages) MUST follow this structure:

### 6.1 YAML Header (top of file)
```yaml
---
Blog: Aliens Blog
Language: EN | HIEN | HI
Title: "<ArticleTitle>"
Slug: "<Article_Slug>"
Author: "Aliens"
Date: "YYYY-MM-DD"
Tags: ["tag1", "tag2", "tag3"]
Description: "<One-line summary of article>"
---
```

### 6.2 Title (H1)
- `# <ArticleTitle>` (in respective language)
- 1-2 line hook paragraph right after title (grab attention)

### 6.3 Introduction
- 2-3 paragraphs explaining what this article covers and why it matters.
- Set the tone (conversational blog style, NOT formal).
- End with a "what you'll learn" or "is article me hum dekhenge" type teaser.

### 6.4 Body Sections (H2/H3)
- Follow the Planning doc's section outline.
- Each H2 section = a major topic area.
- H3 sub-sections where needed for depth.
- Minimum 5 H2 sections (as planned).
- Each section MUST have 3+ paragraphs of substance (no one-liner sections).

### 6.5 Code Examples (if tech topic)
- Use fenced code blocks with language specifier.
- Code MUST be runnable/correct (no pseudo-code unless explicitly marked).
- Brief explanation before and after each code block.

### 6.6 Tables / Comparisons
- At least 1 table in the article body (comparison, pros/cons, etc.).
- Proper Markdown table format.

### 6.7 Conclusion
- 2-3 paragraphs wrapping up.
- Key takeaways (bullet list).
- Personal opinion / recommendation (blog voice).
- Call to action or next steps suggestion.

### 6.8 Footer
- `---`
- `*Published on Aliens Blog*`
- Related article links (if any exist in Blog repo).

---

## [07] Blog Content Quality Rules (Non-Negotiable)

### Style
- Conversational: like talking to a friend who's curious about the topic.
- Opinionated: take a stance, share perspective (unlike neutral Wikipedia).
- Engaging: use hooks, questions, real-world analogies.
- Personal voice: "main", "hum", "tumne kabhi socha?" type addressing.

### Depth
- Beginner-friendly but NOT shallow.
- Explain WHY, not just WHAT.
- Real examples, practical scenarios.
- For tech: show don't just tell (code examples, diagrams described in text).

### Honesty
- No fact invention. Unknown dates/numbers => "not confirmed" mark karo.
- Clearly separate facts from opinions ("meri samajh me", "generally maana jaata hai").
- No plagiarism — everything in original phrasing.

### Enterprise-Grade
- No skeleton/stub articles. Full depth coverage.
- Each section has real content, not filler.
- Minimum: deep enough that reader feels they learned something substantial.

---

## [08] HIEN-Specific Rules (Hinglish Roman)
- Roman script ONLY — Hindi/Devanagari script bilkul NAHI.
- Mix Hindi + English naturally: jaise ek educated Indian English me baat karta hai.
- Technical jargon stays English: API, database, function, class, server, etc.
- Common words in Hindi: "hai", "karta", "hota", "samjho", "dekhte", "chaliye", etc.
- Tone: slightly more casual than EN — desi humor, relatable examples.
- Structure same as EN (same headings, same sections, adapted content).

---

## [09] HI-Specific Rules (Hindi Devanagari)
- Primary script: Hindi Devanagari.
- English technical terms allowed where no standard Hindi translation exists.
  - Example: "API" stays "API", "function" stays "function"
  - But "computer" → "कंप्यूटर", "software" → "सॉफ्टवेयर" (use standard Hindi transliterations)
- Tone: formal-casual (respectful but accessible, like Hindi tech YouTube).
- Structure same as EN (same headings translated, same sections).
- Code blocks: code stays English, but explanations in Hindi.

---

## [10] Update vs Create Logic
- If target article file already exists on disk:
  - Read existing content first.
  - If update/rewrite is needed: follow Strict Update Safety rules.
  - If existing content is good: skip that language version.
- If target article file does NOT exist:
  - Create new file (standard creation).

Normal case: all 3 files are new (first-time creation for this slug).

---

## [11] Acceptance Criteria (Code)
Code step success tabhi:
- Planning doc was read and followed.
- EN article exists at `/Aliens/Blog/EN/{Slug}.md` — non-empty, has YAML header, 5+ H2 sections.
- HIEN article exists at `/Aliens/Blog/HIEN/{Slug}.md` — non-empty, Hinglish Roman (no Devanagari), same structure as EN.
- HI article exists at `/Aliens/Blog/HI/{Slug}.md` — non-empty, Hindi Devanagari, same structure as EN.
- All 3 articles have YAML header with correct metadata.
- No placeholder/stub sections in any article.
- Code examples (if tech topic) are syntactically correct.
