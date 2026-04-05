---
RefId: AliensStyle
Type: ACC_Reference
AppliesTo:
  - WebOS
  - WebSDK
  - WebApp
Language:
  - PHP
  - JS
Status: Active
Version: 26.00.00
LastUpdated: 2026-01-07
Author: "AlienCyborg"
Purpose: >
  ACC workflow reference: the minimal non-negotiable AliensStyle rules required for
  enterprise-grade outputs.
---

# AliensStyle (ACC Reference)

Hard rules (non-negotiable):
- No guessing: if evidence/spec missing, explicitly mark missing; do not invent.
- No feature loss on update unless user explicitly asks.
- Enterprise detail allowed; small/skeleton outputs are a rejection.

Interpretation:
- “Enterprise detail allowed” ka matlab: complete contracts + full sections + production-grade safety/observability.
- “Small/skeleton outputs are a rejection” ka matlab: sirf function stubs / thin wrappers / placeholder bodies forbidden.

## 1) Code-Header (File Header) — Mandatory
For every generated/updated file:
- Must contain a clear header block at top.
- `Author` must be exactly: `AlienCyborg` (no spacing, no alternate name).
- Do NOT copy legacy author from existing file; UPDATE must normalize author.

Header must also include:
- Purpose (1–3 lines)
- Responsibilities (what this file owns)
- Dependencies (internal/external)
- Entry points (public methods/functions)

### JS Engine header minimum
- Include: Name, Version/LastUpdated, Author, Purpose, Highlights, Dependencies.

## 2) Code-Footer — Mandatory
Every file must include a footer section/block:
- Usage guide (practical examples)
- Troubleshooting (common failures + fixes)
- Update notes / changelog (at least for update workflows)

Footer should not be generic; it must reference the real public entry points and expected payloads.

## 3) Sections — Mandatory
- Use explicit `[SECTION]` blocks with rulers.
- Group responsibilities; no giant unstructured blob.

Minimum recommended section set for enterprise targets:
- Boot/Init
- Config
- Contracts (DTO shapes / payload contract)
- Core Logic
- Validation
- Security
- Observability
- Performance
- Helpers

## 4) Function Code-Headers — Mandatory
Every public/exported method/function must start with a function-level header.

### JS method header template
```js
/**
 * Purpose : <one line>
 * Input   : <Payload/Context shape>
 * Output  : <return shape + error envelope>
 */
```

## 5) Logging — Correctness Rules
- Never leak secrets.
- Must be debug-guarded and deterministic.

JS engines:
- `console.*` is forbidden in production code unless it is behind an explicit debug flag.
- Prefer an internal `Debug()` helper that is OFF by default.

PHP engines/modules:
- Use `$echo` string pattern and pass into `d()`/`x()` with guard (`function_exists`).

## 6) Comments — Quality Rules
- Comments must explain "why" and contracts.
- No noise comments ("this is a function" etc).

## 7) Enterprise Gates (Minimum)
- Missing header/footer/function-headers/sections => FAIL.
- File too small for requested scope => FAIL.
- AliensStyle not applied consistently => FAIL.

If response limits force message splitting, the workflow must still ensure the on-disk file is complete and passes the above gates.
