---
RefId: WebOS_System
Type: Reference
Scope: WebOS/PHP.26.00.00/System
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-20
Purpose: >
  WebOS System (non-Engine) functions & load-order ka reference.
  Workflows ko deterministic source dena: System folder ka entrypoint,
  included files, aur kahan se “system helpers” aate hain.
Sources:
  - WebOS/PHP.26.00.00/System/index.php
  - WebOS/PHP.26.00.00/System/*.php
NonNegotiables:
  - NoDelete: true
---

# WebOS_System.md (Reference)

## [01] Canonical Entry
System functions ka include/require load order yahan defined hai:
- `WebOS/PHP.26.00.00/System/index.php`

Current load chain (as per file):
- `0.php`
- `Cache.php`
- `WebOS_Row.php`
- `Row.php`
- `Ecosystem.php`
- `User.php`
- `I.php`
- `Git.php`
- `App.php`
- `Theme.php`
- `Module.php`
- `UID.php`
- `ABCD.php`
- `WebOS.php`

Notes:
- `WebOS/PHP.26.00.00/System/WebOS.php` currently exists but is empty.
- `0.php` is a large utility file (globals/defines + many helper functions).

## [02] How workflows should use this reference
- Agar workflow ko “system layer helpers” ki need ho:
	- pehle `System/index.php` ka load chain samjho
	- phir exact helper ka location identify karo (grep/search recommended)
- Engine layer (WebOS facade) ke liye `WebOS_Engines.md` use karein.

## [03] Safety / Determinism
- Update workflows me system files edit karte waqt:
	- NoDelete
	- anchor-based edits preferred
	- rerun-safe changes only

## [04] Kernel/App.php Runtime Contract (Observed)
System load-chain me `App.php` include hota hai, aur yahin se WebApp runtime ka base context banta hai.

### A) WebApp Enable Gate
- WebApp tabhi run hoti hai jab `MANIFEST['WebApp']['status'] == true`.
- Agar disabled: hard `die('WebApp are disabled...')`.

### B) App Resolve + Cache Envelope Pattern
- App resolve input: `$_SERVER['HTTP_HOST']`.
- App resolve call pattern (observed): `WebOS::Cache('App()', [$Host], ['ttl' => 60000])`.
- Cache result envelope se final output `['o']` key se unwrap hota hai.
- Same envelope pattern features/libraries ke liye bhi use hota hai:
	- `WebOS::Cache('App_Features()', [$Argss], ['ttl' => 60000])`
	- `WebOS::Cache('Theme_Libraries()', [$ArgsTL], ['ttl' => 60000])`

### C) Version + SDK Version Selection
- Request override: `$_REQUEST['version']`.
- Beta selection (observed): if `version == 'beta'` OR `version == App['beta']`:
	- `App['version'] = App['beta']`
	- `SDK_Version = App['websdk_beta']`
- Else:
	- `SDK_Version = App['websdk']`

### D) Theme + Role Resolution
- Theme: default `App['theme']`.
- Role: `App['role']` fallback `'User'`.

### E) Directory Map (App/Brand/SDK/WebOS)
- Runtime builds `App['DIR']` for App Theme, Brand Theme, SDK Module, WebOS paths.
- Branch fallback behavior exists ("*_Default" keys) for default branches.

### F) App Constants
- Defines: `app`, `App`, `S`, `V`, `SD` (runtime constants used across stack).
