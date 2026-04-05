---
WorkflowId: Blog_ACLE-Documentation
Type: Workflow_Singular-Documentation
Domain: Blog
Category: Blog Article Video Generation
Language: Python
Status: Draft
Version: 26.03.00
LastUpdated: 2026-03-15
EntryPoint: false
ParentWorkflow: Blog_ACLE
Purpose: >
  Singular Documentation workflow: given a single original Blog article path, uski ACLE conversion ka
  documentation generate karo — covering article overview, conversion results,
  quality metrics, aur usage guide. Documentation file Docs/Blog_ACLE/ me save hoti hai.
Invocation:
  Signature: Workflow("Blog_ACLE-Documentation", "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura", "Document the ACLE Blog conversion results")
  TargetsMeaning: >
    Single article path relative to /Aliens/Blog/ without .md extension.
    Comma list is invalid (singular workflow).
    Example: "HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura" implies conversion results at
    "/Aliens/Blog_ACLE/HIEN/Software_Engineers_Ke_Liye_AI_Acha_Hai_Ya_Bura.html".
  DescriptionMeaning: >
    Human language constraints. Must not expand scope. If empty/missing -> fail fast.
Outputs:
  Documentation:
    Path: "/Aliens/Docs/Blog_ACLE/"
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
  - Source Blog repo is READ-ONLY.
  - This workflow does NOT run any conversion. It only documents what happened.
  - ArticleSlug = ArticlePath with `/` replaced by `_`.
---

# Blog_ACLE-Documentation

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
- Source Blog repo modify karna forbidden
- Conversion output files modify karna forbidden
- Converter scripts modify karna forbidden

# [04] Inputs
Targets:
- MUST be exactly ONE article path
- Relative to `/Aliens/Blog/` without `.md` extension
- Comma-separated list => error (`WF_TARGETS_INVALID`)

Description:
- Required
- If empty/missing => fail fast with `ACC_ERR_DESCRIPTION_REQUIRED`

# [05] Manifest Gate (Mandatory)
Read manifest: "/Aliens/manifest.json"
- Documentation write permission (allowed by default; writes to `/Aliens/Docs/`).

# [06] Discovery Phase (Mandatory — Data from Disk)

## [06.1] Source Article Discovery
Read: `/Aliens/Blog/{ArticlePath}.md`
- Article title, word count, section count
- Language (from path: EN/HIEN/HI)
- Tables count, code blocks count

## [06.2] Conversion Results Discovery
### HTML Check:
- Path: `/Aliens/Blog_ACLE/{ArticlePath}.html`
- Exists? Size? (must be > 5KB for success)
- Branding check: "Aliens Blog" present?

### Narrations JSON Check:
- Path: `/Aliens/Blog_ACLE/{Language}/{ArticleName}_narrations.json`
- Exists? Valid JSON? Narration count?

### Audio Check:
- Path: `/Aliens/Blog_ACLE/{Language}/audio_{ArticleName}/`
- Directory exists? MP3 count? Total size?

# [07] Documentation Output Path
- `/Aliens/Docs/Blog_ACLE/{ArticleSlug}.md`
- ArticleSlug = ArticlePath with `/` replaced by `_`

# [08] Documentation Format
Documentation MUST include:
1) Article Overview (title, language, source path)
2) Conversion Results (HTML size, slide count, audio count)
3) Quality Check (branding, structure, completeness)
4) File Inventory (all generated files with paths + sizes)
5) Notes (any issues, suggestions)

# [09] Acceptance Criteria
Documentation success tabhi:
- Output file exists and non-empty
- All discovery sections present
- Conversion results accurately reflect disk state
