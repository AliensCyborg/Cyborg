---
WorkflowId: Blog_Article-Planning
WorkflowName: Aliens Blog Article Planner
Type: Workflow_Singular-Planning
ParentWorkflowId: Blog_Article
Domain: Blog
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
Inputs:
  - Targets: "Article_Slug (single only)"
  - Description: "REQUIRED; topic context + requirements"
Produces:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/planning.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
    - "Workflows/ACC/_Common/Workflow_Singular-Planning.md"
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
---

# Blog_Article-Planning.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`
3) require_once `Workflows/ACC/_Common/Workflow_Singular-Planning.md`

---

## [01] Purpose (Hinglish)
Yeh workflow ek single blog article ke liye **planning / outline** banata hai.
Planning ka goal:
- Article ko blog style me **structured outline** dena (sections, key points, flow)
- Teeno languages ka plan banana (EN primary, HIEN/HI adapted)
- Tone + audience + depth decide karna
- Key topics aur talking points extract karna
- Missing info aur assumptions clearly mark karna (no guessing)

> Note: Planning ka output final article nahi hota. Final article `Blog_Article-Code` likhega.

---

## [02] Inputs Rules (Strict)

### Targets
- `Targets` MUST be a single article slug.
- Multiple targets (comma-separated) yahan allowed nahi.
- If multiple targets detected: fail fast with `ACC_ERR_ARGS_INVALID`.

### Description
- REQUIRED hai. Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.
- Description me topic ka context, audience, tone, depth sab aana chahiye.
- Agar outer workflow (CSV mode) ka context diya gaya ho, to is planning me same as-is include karo.

---

## [03] Slug Normalization (Deterministic)

### 3.1 Article Slug
- `Article_Slug` = Targets trimmed.
- Already PascalCase_With_Underscores format me hona chahiye.
- Case preserve.

### 3.2 Title Extraction
- If `Description` contains a line like `Title: <value>` (case-insensitive key):
  - Use that `<value>` as human-readable `ArticleTitle`.
- Else:
  - Derive `ArticleTitle` from `Article_Slug`: underscores → spaces.
  - Example: `Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura` → `Software Engineers Ke Liye AI Acha Hai Ya Bura`

---

## [04] Planning Output Path (SSOT)
- Planning doc MUST be saved at:
  - `/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md`

---

## [05] Planning Document Format (Mandatory)
Planning doc MUST contain these sections (in order):

### 1) Title + Metadata
- Article title, slug, date, author (AlienCyborg / Aliens Blog)

### 2) Prompt Payload Snapshot
- Full Description as-is (for traceability)

### 3) Audience + Reading Level
- Who is this for? (beginners, developers, general tech audience, etc.)
- Reading level (simple, intermediate, deep)

### 4) Blog Article Structure Plan
- Overall flow: hook → body → conclusion
- Estimated section count
- Tone decision (casual, semi-formal, opinionated, tutorial)

### 5) Section Outline (H2/H3)
- Each planned H2 section with 2-3 bullet points of what it covers
- Suggested H3 sub-sections where needed
- Minimum: 5 meaningful H2 sections planned

### 6) Key Points / Talking Points
- 10-15 key points the article MUST cover
- Mark which are facts vs opinions vs analysis

### 7) Code Examples Plan (if tech topic)
- What code snippets to include (language, purpose, complexity)
- Skip this section if topic is non-technical

### 8) Tables / Comparison Plan
- At least 1 comparison table planned (pros/cons, alternatives, etc.)
- Table structure (columns, expected rows)

### 9) Internal Link Plan
- Related Aliens Blog articles to cross-reference
- Suggested further reading topics

### 10) Multilingual Adaptation Notes
- EN: primary, written from scratch
- HIEN: what tone/style adaptations for Hinglish Roman
- HI: what tone/style adaptations for Hindi Devanagari
- Terms that should stay English across all versions (technical jargon)

### 11) Assumptions + Unknowns
- What facts need verification
- What the article explicitly does NOT cover (scope boundary)

### 12) Acceptance Criteria
- What "done" means for this specific article

---

## [06] Content Rules (Non-Negotiable)
- Blog style — NOT Wikipedia/encyclopedia formal style.
- Conversational, engaging, personal voice (like talking to a friend).
- Honesty: agar exact fact confirm nahi, explicitly label it as `Not confirmed`.
- No plagiarism: content original phrasing me ho.
- Topic ka depth Description ke mutabiq ho — agar "deep dive" bola to deep dive.

---

## [07] Acceptance Criteria (Planning)
Planning success tabhi:
- Output file exists and non-empty at correct path
- All 12 sections present (section 7 optional for non-tech topics)
- At least 5 H2 sections planned
- At least 10 key points listed
- Multilingual notes present
- Assumptions section present
