---
RefId: PyOS_System
Type: Reference
Scope: PyOS/Py.26.00.00/System
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-20
Purpose: >
  PyOS System (non-Engine) functions & load-order ka reference.
  Workflows ko deterministic source dena: System folder ka entrypoint,
  included files, aur kahan se “system helpers” aate hain.
Sources:
  - PyOS/Py.26.00.00/System/index.py
  - PyOS/Py.26.00.00/System/*.py
NonNegotiables:
  - NoDelete: true
---

# PyOS_System.md (Reference)

## [01] Canonical Entry
System functions ka include/require load order yahan defined hai:
- `PyOS/Py.26.00.00/System/index.py`

Current load chain (as per file):
- `0.py`
- `Cache.py`
- `PyOS_Row.py`
- `Row.py`
- `Ecosystem.py`
- `User.py`
- `I.py`
- `Git.py`
- `App.py`
- `Theme.py`
- `Module.py`
- `UID.py`
- `ABCD.py`
- `PyOS.py`

Notes:
- `PyOS/Py.26.00.00/System/PyOS.py` currently exists but is empty.
- `0.py` is a large utility file (globals/defines + many helper functions).

## [02] How workflows should use this reference
- Agar workflow ko “system layer helpers” ki need ho:
	- pehle `System/index.py` ka load chain samjho
	- phir exact helper ka location identify karo (grep/search recommended)
- Engine layer (PyOS facade) ke liye `PyOS_Engines.md` use karein.

## [03] Safety / Determinism
- Update workflows me system files edit karte waqt:
	- NoDelete
	- anchor-based edits preferred
	- rerun-safe changes only

## [04] Kernel/App.py Runtime Contract (Observed)
System load-chain me `App.py` include hota hai, aur yahin se PyApp runtime ka base context banta hai.

### A) PyApp Enable Gate
- PyApp tabhi run hoti hai jab `MANIFEST['PyApp']['status'] == true`.
- Agar disabled: hard `die('PyApp are disabled...')`.

### B) App Resolve + Cache Envelope Pattern
- App resolve input: `$_SERVER['HTTP_HOST']`.
- App resolve call pattern (observed): `PyOS::Cache('App()', [$Host], ['ttl' => 60000])`.
- Cache result envelope se final output `['o']` key se unwrap hota hai.
- Same envelope pattern features/libraries ke liye bhi use hota hai:
	- `PyOS::Cache('App_Features()', [$Argss], ['ttl' => 60000])`
	- `PyOS::Cache('Theme_Libraries()', [$ArgsTL], ['ttl' => 60000])`

### C) Version + SDK Version Selection
- Request override: `$_REQUEST['version']`.
- Beta selection (observed): if `version == 'beta'` OR `version == App['beta']`:
	- `App['version'] = App['beta']`
	- `SDK_Version = App['PySDK_beta']`
- Else:
	- `SDK_Version = App['PySDK']`

### D) Theme + Role Resolution
- Theme: default `App['theme']`.
- Role: `App['role']` fallback `'User'`.

### E) Directory Map (App/Brand/SDK/PyOS)
- Runtime builds `App['DIR']` for App Theme, Brand Theme, SDK Module, PyOS paths.
- Branch fallback behavior exists ("*_Default" keys) for default branches.

### F) App Constants
- Defines: `app`, `App`, `S`, `V`, `SD` (runtime constants used across stack).
