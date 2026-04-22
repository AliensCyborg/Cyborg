---
RefId: PyOS_Engines
Type: Reference
Scope: PyOS/Py.26.00.00
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-20
Purpose: >
  PyOS Engines ka authoritative reference yahan maintain hota hai.
  Is file ka goal: workflows ko ek deterministic, non-guessing entry point dena
  jahan se woh PyOS engines ka index + routing rules pakad sake.
Sources:
  - PyOS/Py.26.00.00/Engine/PyOS_engines_generated.md
NonNegotiables:
  - NoDelete: true
---

# PyOS_Engines.md (Reference)

## [01] Canonical Source
PyOS engines ka generated index yahan hai:
- `PyOS/Py.26.00.00/Engine/PyOS_engines_generated.md`

Is generated file me:
- Quick Index (engine name → anchor)
- Engine purpose summaries
- (Usually) engine ke internal docs / comment blocks

Workflows ko **engine list hardcode nahi** karni chahiye — hamesha is source ko reference karein.

## [02] Routing Rules (Workflow Guidance)
- Engine calls facade style me hoti hain: `PyOS::<Engine>()` ya `PyOS::<Engine><Action>()`.
- Engines ka physical location: `PyOS/Py.26.00.00/Engine/PyOS.<Engine>.py`.
- Workflow updates me:
	- NoDelete: always
	- Modify-only policy: update workflows me missing target => error
	- Determinism: stable ordering + rerun-safe edits

## [03] Repo Reality Notes
- Detailed per-engine listing intentionally yahan duplicate nahi kiya gaya.
- Agar aapko engine inventory chahiye, open `PyOS_engines_generated.md`.

## [04] Boundary Rules (Engines vs Modules) — SSOT
PyOS ecosystem me JS/Python code ka boundary strict hai:

### A) PyOS Engines (aka `libraries`)
- Purpose: reusable utilities/features (auth, db, cache, ui helpers, loaders, etc.)
- DB relation: direct “content tables” relation **nahi** hota (feature-only role)
- Allowed deps: Engine -> Engine (allowed)
- Forbidden deps: Engine -> Module (forbidden)
	- Applies to: Python Engines + JS Engines

### B) PySDK Modules (aka `modules`)
- Purpose: domain/content code jiska kahin na kahin SQL/DB/API se link hota hai (User, Product, Student…)
- Allowed deps:
	- Module -> Engine (allowed)
	- Module -> Module (allowed)
		- Applies to: Python Modules + JS Modules
- Forbidden deps:
	- Engine -> Module (still forbidden)

### C) PyApp JS Rule
- PyApp files ke andar custom JS likhna **allowed nahi**.
- Agar PyApp ko JS chahiye:
	1) First priority: PySDK Module JS
	2) Agar reusable “big feature” ho: PyOS Engine

## [05] PyOS_manifest (AppHead loader intent)
`AppHead([... 'PyOS_manifest' => [...] ])` ke andar `PyOS_manifest` ka role:
- `libraries`: load list of PyOS engines + third-party libs (example: `jQuery`, `PyOS`, `PyOS.DB`, `PyOS.Screen`, `0`)
- `modules`: load list of PySDK modules for the app/session (example: `User`, `Product`)

Runtime loader contract (observed in `Kernel/App.py` -> `AppHeadPyOSLoader()`):
- `window.PyOS_manifest` is emitted as JSON.
- Loader is imported as ES module from `LoaderConfig.Url` (default: `/?js=PyOS.Loader`).
- Loader config is emitted as JSON (`LoaderConfig`) and mapped to loader runtime keys:
	- `enforce_integrity` <= `LoaderConfig.EnforceIntegrity`
	- `max_memory_items`  <= `LoaderConfig.MaxMemoryItems`
	- `retry`             <= `LoaderConfig.Retry`
	- `base`              <= `LoaderConfig.Base`
	- `cache_enabled`     <= `LoaderConfig.CacheEnabled`
	- `version_param`     <= `LoaderConfig.VersionParamKey + '=' + (manifest.version || '')`
	- `param`             <= `LoaderConfig.Param` (default: `import`)
- `PyOS_Loader.init_manifest(window.PyOS_manifest)` is awaited.
- Preload strategy is slice-based (Now/Visible/Idle) using `PyOS_Loader.smart_preload()` on `manifest.modules`.
- If `LoaderConfig.PreloadLibraries=true`, then `PyOS_Loader.preload_libraries(manifest.libraries)` is awaited.

Integrity fields (manifest-level):
- `manifest.hashes` + `manifest.dependencies` + `manifest.signature` are part of the schema emitted by defaults.
- `LoaderConfig.EnforceIntegrity` default is `false` (integrity is opt-in; enable only with real hashes/signature).

Note (ordering):
- Current runtime schedules module smart-preloads first and then (optionally) awaits library preloading.
- Dependency correctness must be enforced via boundaries + loader dependency resolution (do not rely on implicit order).

## [06] Engine Envelope Contract (Observed)
Engines should return deterministic, machine-checkable envelopes.

Minimum recommended keys:
- `success`: boolean
- `engine`: engine name (string)

On success (example pattern):
- `success: true`, `engine: '<Name>'`, plus action/result fields.

On error (example pattern):
- `success: false`
- `error`: stable error type string
- `meta`: array of context (e.g., action, key)
- `engine`: engine name

EngineRouter payload compatibility rule:
- Engine actions should safely unwrap payloads where router passes numeric args list.
