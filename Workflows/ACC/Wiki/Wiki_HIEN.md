---
WorkflowId: Wiki_HIEN
WorkflowName: Aliens Wiki Article Workflow (HIEN)
Type: Workflow_Plural
Domain: Wiki
Status: Draft
Version: 26.00.00
LastUpdated: 2026-02-27

Intent:
	Summary: >
		Aliens Wiki ke liye Wikipedia-style ultra detailed articles generate karna (Hinglish Roman).
		Workflow single-topic mode aur CSV/planner mode dono support karta hai, lekin per-run sirf 1 article.
	WhenToUse:
		- "Workflow('Wiki_HIEN','Browser','...')" (single article)
		- "Workflow('Wiki_HIEN','Articles.csv','...')" (planner se one-article-per-run)

Inputs:
	Targets:
		Format: "TopicName | Articles.csv"
		Examples:
			- "Browser"
			- "Web Browser"
			- "Articles.csv"
	Description:
		Format: "REQUIRED; any human language. Constraints + clarity."
		Examples:
			- "Yeh wiki article actual me Web Browser k baare me honay wala hai. Beginner-friendly, ultra detailed."
			- "Articles.csv me bahut saary articles hain; har run me sirf 1 pending article likho."

Defaults:
	Language: "HIEN (Hinglish Roman)"
	OutputPreference:
		Primary: "/Aliens/Wiki/HIEN"
		Fallback: "/Aliens/Docs/Wiki/HIEN"

Permissions:
	NoDelete: true
	CanCreateFiles: true
	CanEditFiles: true
	CanModifyProd: false
	CanTouchDB: false

Approval:
	Mode: "manifest"
	RequiredActions: []

Requires:
	Common:
		- "Workflows/ACC/_Common/Workflow_Universal.md"
		- "Workflows/ACC/_Common/Workflow_Plural.md"
	Refs:
		- "Workflows/ACC/_Refs/ACC.Targets.md"
		- "Workflows/ACC/_Refs/ACC.Paths.md"
		- "Workflows/ACC/_Refs/ACC.Output.md"
		- "Workflows/ACC/_Refs/ACC.Safety.md"
		- "Workflows/ACC/_Refs/ACC.Manifest.md"
		- "Workflows/ACC/_Refs/ACC.Errors.md"

Calls:
	ChildWorkflows:
		- "Wiki_HIEN-Planning"
		- "Wiki_HIEN-Documentation"

Outputs:
	RunRecord: true
	Planning: true
	Code: false
	Documentation: true

NonNegotiables:
	- NoDelete: true
	- OneArticlePerRun: true
	- OneTopicOnlyInNonCsvMode: true
	- Language: "Hinglish Roman only; Hindi script not allowed"
	- WikipediaStyle: true
	- BeginnerFriendly: true
	- NoFactInvention: true
	- NoNetworkCalls: true
---

# Wiki_HIEN.md

## [00] Universal Includes (Mandatory Order)
1) require_once `Workflows/ACC/_Common/Workflow_Universal.md`
2) require_once `Workflows/ACC/_Common/Workflow_Plural.md`

---

## [01] Workflow ka meaning (Hinglish)
`Wiki_HIEN` ek **orchestrator workflow** hai.
Iska kaam:
- Targets ko validate karna
- Decide karna ke run **single-topic** hai ya **CSV/planner** mode
- Per-run sirf **1 article** generate karwana
- Planning + Documentation child workflows call karna
- RunRecord outputs contract follow karna

---

## [02] Supported Run Modes (Strict)

### Mode A: Single Topic
Example:
`Workflow('Wiki_HIEN', 'Browser', '...')`

Rules:
- Targets MUST be exactly one topic.
- Comma-separated multiple topics forbidden.
	- If comma detected => fail fast with `ACC_ERR_ARGS_INVALID`.

Execution:
- Call `Wiki_HIEN-Planning` with same Targets + Description
- Call `Wiki_HIEN-Documentation` with same Targets + Description

### Mode B: CSV / Planner
Example:
`Workflow('Wiki_HIEN', 'Articles.csv', '...')`

Rules:
- CSV file MUST be resolved ONLY from:
	- `/Aliens/Project/{Developer_Username}/Articles.csv`
- Per-run **sirf 1 runnable row** process hogi (global governance).

---

## [03] CSV Schema (SSOT for Wiki_HIEN)
`Articles.csv` MUST be a planner-style CSV (case-insensitive headers).

### Required columns
- `ID`
- `Name`  (topic title)
- `UTag`  (output file-name tag/slug; used for `{TopicSlug}.md`)
- `Worklflow`  (MUST be `Wiki_HIEN`)
- `Description`  (row prompt payload; REQUIRED)
- `Status`  (`0` pending, `1` success, `2` failed)
- `Assign`  (MUST equal `{Developer_Username}`)

### Optional (recommended)
- `DependsOn`  (ID references)
- `Prority`
- `created`, `modified`
- `Content_Path`  (final article path override; must still be inside AllowedRoots)
- `Planning_Path` (planning path override)
- `Doc_Path` (doc path override)
- `Planning`, `Planning_Save`, `Docs`, `Doc_Save` (phase markers)

If schema mismatch / required column missing:
- Fail fast with `ACC_ERR_CSV_SCHEMA_INVALID`.

---

## [04] CSV Row Picking Rules (Deterministic)
Runnnable row conditions (same philosophy as `Workflows` universal runner):
- `Status == 0`
- `Assign == {Developer_Username}`
- `DependsOn` empty OR all referenced rows have `Status == 1`

Pick strategy:
- File order me first runnable row pick karo.
- Random selection forbidden.

If no runnable row:
- Return `noop` or `blocked` (non-destructive).

---

## [05] CSV Dispatch Rules (Important)
For picked row:
- Validate:
	- `Worklflow` == `Wiki_HIEN`
	- `Name` non-empty
	- `UTag` non-empty
	- `Description` non-empty
	- Missing row description => `ACC_ERR_CSV_DESCRIPTION_MISSING` and row ko mutate mat karo.

Then execute **same run** as:
- `Targets = Row.Name` (title source)
- `EffectiveDescription = "Title: " + Row.Name + "\nUTag: " + Row.UTag + "\n\n" + OuterDescription + "\n\n---\n\n" + Row.Description`

Important:
- CSV mode me **article title** = `Name`
- CSV mode me **output file name** = `UTag` (as slug). Example: `UTag=web-browser` => `web-browser.md`

Child calls:
- `Wiki_HIEN-Planning( Targets, EffectiveDescription )`
- `Wiki_HIEN-Documentation( Targets, EffectiveDescription )`

Writeback semantics (strict):
- `Status` ka matlab row fully complete hai.
- Planning success => `Planning=1`, but `Status` stays `0` until docs complete.
- Docs success => `Docs=1` and `Status=1`.
- Any stage fail => mark that stage `2` and set `Status=2`.

---

## [06] Output Contract (Wiki Article)
Article output path policy (deterministic):
- Default article path:
	- Preferred: `/Aliens/Wiki/HIEN/{TopicSlug}.md`
	- Fallback: `/Aliens/Docs/Wiki/HIEN/{TopicSlug}.md`

`{TopicSlug}` resolution:
- CSV mode: `{TopicSlug}` MUST come from `UTag` column.
- Non-CSV mode: `{TopicSlug}` MUST be derived from `Targets`.

Hard rule:
- Output path MUST be within manifest `Permissions.AllowedRoots`.
- Agar user `/Aliens/Wiki/HIEN` chahte hain, lekin manifest AllowedRoots me `/Aliens/Wiki` missing ho:
	- Fallback to `/Aliens/Docs/Wiki/HIEN`
	- OR fail with actionable error (workflow decide based on Description strictness).

Planning path default:
- `/Aliens/.Alien/{Developer_Username}/Planning/Wiki/HIEN/{TopicSlug}.plan.md`

---

## [07] Content Quality Rules (Non-Negotiable)
- Hinglish Roman only; Hindi script bilkul nahi.
- Wikipedia-style headings, lead, infobox, sections.
- Tables + Mermaid diagrams mandatory (details child doc workflow me).
- Facts invent mat karo. Unconfirmed cheezen `Not confirmed` label karo.
- No network calls: agar external exact links verify nahi, to guess mat karo.

