---
WorkflowId: Blog_Article-Documentation
WorkflowName: Aliens Blog Article Documentation
Type: Workflow_Singular-Documentation
ParentWorkflowId: Blog_Article
Domain: Blog
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
Inputs:
  - Targets: "Article_Slug (single only)"
  - Description: "REQUIRED; topic context"
Consumes:
  - PlanningDoc: "/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md"
  - EN_Article: "/Aliens/Blog/EN/{Article_Slug}.md"
  - HIEN_Article: "/Aliens/Blog/HIEN/{Article_Slug}.md"
  - HI_Article: "/Aliens/Blog/HI/{Article_Slug}.md"
Produces:
  - DocumentationDoc: "/Aliens/Docs/Blog/{Article_Slug}.doc.md"
  - RunRecordItem: "/Aliens/.Alien/{Developer_Username}/Runs/{RunId}/Items/{RunItemId}/documentation.md"
Requires:
  Common:
    - "Workflows/ACC/_Common/Workflow_Universal.md"
    - "Workflows/ACC/_Common/Workflow_Singular.md"
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
  - AllLanguagesMustExist: true
---

# Blog_Article-Documentation.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Singular.md`

---

## [01] Purpose (Hinglish)
Is workflow ka kaam: blog article creation ka **documentation record** banana.
Yeh article likhne ke baad chalta hai — verify karta hai ke teeno language versions exist
karte hain aur ek summary doc create karta hai for future reference.

---

## [02] Inputs Rules (Strict)

### Targets
- Single article slug only.
- Multiple targets => `ACC_ERR_ARGS_INVALID`.

### Description
- Required. Empty => `ACC_ERR_DESCRIPTION_REQUIRED`.

---

## [03] Dependencies (Mandatory)
Yeh workflow tabhi run hoga jab:
- Planning doc exists: `/Aliens/.Alien/{Developer_Username}/Planning/Blog/{Article_Slug}.plan.md`
- EN article exists: `/Aliens/Blog/EN/{Article_Slug}.md`
- HIEN article exists: `/Aliens/Blog/HIEN/{Article_Slug}.md`
- HI article exists: `/Aliens/Blog/HI/{Article_Slug}.md`

If any missing:
- Fail fast with `ACC_ERR_CODE_GENERATION_FAILED` (code step incomplete).

---

## [04] Output Path (SSOT)
- Documentation doc MUST be saved at:
  - `/Aliens/Docs/Blog/{Article_Slug}.doc.md`

---

## [05] Documentation Format (Mandatory)
Documentation doc MUST contain these sections:

### 1) Header
```markdown
---
Doc: Blog Article Documentation
Article: "<ArticleTitle>"
Slug: "<Article_Slug>"
Date: "YYYY-MM-DD"
Workflow: Blog_Article
---
```

### 2) Article Summary
- 2-3 line summary: what the article is about.
- Topic, audience, key angle/perspective.

### 3) Files Created
- List all files with paths:
  - Planning: `/Aliens/.Alien/{Dev}/Planning/Blog/{Slug}.plan.md`
  - EN: `/Aliens/Blog/EN/{Slug}.md`
  - HIEN: `/Aliens/Blog/HIEN/{Slug}.md`
  - HI: `/Aliens/Blog/HI/{Slug}.md`
  - Documentation: `/Aliens/Docs/Blog/{Slug}.doc.md`

### 4) Content Stats (per language)
For each language version:
- File size (approximate lines/words)
- Number of H2 sections
- Number of H3 sub-sections
- Code blocks count (if any)
- Tables count

### 5) Quality Checklist
- [ ] EN article: YAML header present
- [ ] EN article: 5+ H2 sections
- [ ] EN article: No placeholder sections
- [ ] HIEN article: Hinglish Roman (no Devanagari)
- [ ] HIEN article: Same structure as EN
- [ ] HI article: Hindi Devanagari
- [ ] HI article: Same structure as EN
- [ ] All articles: code examples correct (if applicable)

### 6) Cross-References
- Related articles in Blog repo (if any)
- Topics that could be future articles (from this article's content)

### 7) Notes
- Any issues encountered during creation
- Assumptions made
- Suggestions for future updates

---

## [06] Acceptance Criteria (Documentation)
Documentation success tabhi:
- Documentation file exists at `/Aliens/Docs/Blog/{Slug}.doc.md` and non-empty
- All sections present
- All 3 language files verified as existing on disk
- Quality checklist filled in (not just placeholders)
