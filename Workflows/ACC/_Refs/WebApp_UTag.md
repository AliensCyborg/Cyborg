---
RefId: WebApp.UTag
Type: ReferenceRules
Domain: WebApps
Scope: UTag (unique HTML tag) generation + global uniqueness
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-16
Purpose: >
  Define UTag rules and the only allowed strategy to guarantee that UTags never collide
  across the entire WebOS ecosystem (thousands of WebApps, many machines).
NonNegotiables:
  - UTagIsHtmlTag: true
  - UTagGlobalUniqueAcrossEcosystem: true
  - UTagOnlyFor: [Screen, Section, Component]
  - NoManualUTagSelection: true
  - AllocationMustBeAutomatic: true
  - NoDbOnClientMachines: true
  - NoLiveSyncRequired: true
...

# WebApp — UTag (Global Uniqueness SSOT)

## [0] Definition
- **UTag** is a short HTML tag name used as the unique wrapper/root for:
  - Screen scope wrapper
  - Section scope wrapper
  - Component root wrapper

In the ecosystem, UTag collisions are forbidden:
- No two different UI artifacts may share the same UTag
- Not just within an app — across **all** WebApps in WebOS ecosystem

---

## [1] Where UTag is Allowed
UTag exists only for:
- Screen
- Section
- Component

Do not invent UTag for other workflow rows (APIs, docs, planning-only rows, research rows).

---

## [2] UTag Format (SSOT)
UTag has two generations:

### 2.1 Legacy UTag v1 (backward compatibility only)
- **Singular**: 4 lowercase letters (example: `cwco`)
- **Plural**: 5 lowercase letters where 5th is `s` (example: `trcxs`)

v1 rule:
- Allowed ONLY when updating/maintaining an already-existing artifact that already uses v1.
- New work MUST NOT mint new v1 tags (space too small for ecosystem-wide collision-free growth).

### 2.2 UTag v2 (required for new work)
Because you explicitly require:
- no DB on 1000+ PCs
- no live sync
- and no human/manual insertion

Then the only feasible strategy is **offline automatic minting with extremely large namespace**.

UTag v2 format (recommended):
- **Singular**: 12 chars, lowercase base36, must start with a letter:
  - Pattern: `u` + 11 chars from `[a-z0-9]` (example: `u3k9m1q8p0az`)
- **Plural**: 13 chars where last char is `s`:
  - Pattern: `u` + 11 chars from `[a-z0-9]` + `s` (example: `u3k9m1q8p0azs`)

Reason:
- HTML tag names allow digits after the first character.
- This makes collisions astronomically unlikely without any coordination.

Validation rules:
- Lowercase only (v1): `a-z`
- Lowercase base36 (v2): first char `a-z`, remaining `[a-z0-9]`
- No spaces, no underscores, no uppercase
- UTag must not match reserved skeleton tags: `bl`, `hd`, `bd`, `ft`

Note:
- In HTML, unknown tags are allowed and used here as scoped wrappers.

---

## [3] Global Uniqueness Without DB / Without Live Sync
Hard truth (engineering constraint):
- If there is **no shared authority** (no DB, no shared service, no live sync), then a mathematical 100% guarantee for a *small* namespace (like 4 letters) is impossible.

What we CAN do (and what this SSOT requires):
- Move to UTag v2 (huge namespace)
- Mint automatically with cryptographic randomness
- Keep a local “already used” registry to prevent accidental reuse on the same machine

Result:
- Practical guarantee: collisions become effectively impossible at ecosystem scale.

---

## [4] Required Allocation Mechanism (Automatic, No Human)
UTag insertion/selection is never done by a human.

### 4.1 Generator contract
Generator must:
- Produce v2 UTags for new artifacts
- Ensure it never returns a tag already used on the same machine
- Persist a local registry (append-only)

### 4.2 Local registry (no sync)
Store locally under developer workspace:
- `/Aliens/.Alien/{Developer}/Config/UTag.Registry.jsonl`

Each line (JSONL) must record:
- `utag`
- `artifact_type` (`screen|section|component`)
- `app_name`
- `artifact_name` (normalized)
- `created_at`
- `created_by`

This is not for global sync; it is for local determinism + audit.

---

## [5] Multi-PC Strategy (1000+ PCs, No DB, No Live Sync)
Policy:
- Use UTag v2 only for new work.
- Mint tags locally using secure randomness.
- Record every minted tag in local registry.

Optional (non-live) safety net:
- When code eventually merges to a central repo, a CI audit can scan all WebApps for duplicate UTags.
- If a duplicate is detected (extremely unlikely), it must be resolved by renaming one artifact's UTag via update workflow.

---

## [6] Workflow Integration Contract
- Planning workflows for Screen/Section/Component must mint/record UTag automatically (no human input).
- Code workflows must use the UTag exactly as planned.
- Planner CSV:
  - `utag` column must be populated only for Screen/Section/Component rows.
  - For those rows, it is required and must never be blank.

---

## [7] Auditability
Every UTag allocation must be traceable:
- who allocated it
- when
- for which app + artifact
- from which machine

This is required for ecosystem-level auditing.
