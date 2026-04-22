---
WorkflowId: Attendance_Dashboard
Type: Workflow_Utility
Domain: Attendance
Category: Attendance — Dashboard Rebuild (Full Scan, Idempotent)
Status: Draft
Version: 26.04.00
LastUpdated: 2026-04-19
EntryPoint: true
Purpose: >
  Rebuild BOTH the repo-level `/Aliens/Attendance/Dashboard.md` and every
  per-cyborg `/Aliens/Attendance/Cyborg/{L1}/{L2}/{CyborgID}/Dashboard.md`
  from the append-only JSONL ledgers.

  `Attendance` workflow ke append step me sirf **incremental** updates hote hain
  (jis cyborg ne abhi event append kiya). Is workflow se **full-scan rebuild**
  hota hai — top-10 leaderboards, new-joiner panels, data-integrity checks,
  stale counters — sab ek consistent snapshot se regenerate.

  Safe to run any time. Stateless. Pure read → compute → overwrite derived
  markdown + `_index.json` counters. History JSONL files NEVER touched.
Invocation:
  Signature:
    - Workflow("Attendance_Dashboard", "ALL", "Scheduled rebuild (weekly)")
    - Workflow("Attendance_Dashboard", "C1071", "Rebuild single-cyborg dashboard")
    - Workflow("Attendance_Dashboard", "ALL", "Post-Check-in refresh")
  TargetsMeaning: >
    - `ALL`     = rebuild repo Dashboard.md + every per-cyborg Dashboard.md
    - `{CID}`   = rebuild only that cyborg's per-cyborg Dashboard.md (cheap path)
    - `{CID1,CID2,...}` = rebuild listed cyborgs + repo Dashboard
    Empty/missing => `ACC_ERR_TARGETS_REQUIRED`.
  DescriptionMeaning: >
    Free-text reason. Required. Empty => `ACC_ERR_DESCRIPTION_REQUIRED`.
ChildWorkflows: []
Safety:
  NoDelete: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: false
  NeverTouchJSONL: true
---

# Attendance_Dashboard

# [00] Philosophy (NON-NEGOTIABLE)
```
READ-ONLY for JSONL history. WRITE-ONLY for derived markdown + _index counters.
```
- Ledger JSONL files (`Cyborg/{L1}/{L2}/{CID}/{YYYY}/{YYYY-MM-DD}.jsonl`) **never** edited.
- `Profile.json` also **not** touched here (that is `Attendance` workflow's scope).
- This workflow rewrites only:
  - `/Aliens/Attendance/Dashboard.md` (repo-level)
  - `/Aliens/Attendance/Cyborg/{L1}/{L2}/{CID}/Dashboard.md` (per-cyborg)
  - `/Aliens/Attendance/_index.json` (counters/top-N cache — optional)

# [01] Strict Scope Lock
Allowed:
- Read every `*.jsonl` under `Attendance/Cyborg/**` (recursive)
- Read every `Profile.json` under `Attendance/Cyborg/**`
- Compute aggregates in memory (total events, daily/hourly/weekday histograms, top-10 tables, new-joiners, data-integrity counters)
- Overwrite the 2 dashboard .md files listed above
- Overwrite `_index.json` counter block (additive keys only; never delete keys)

Forbidden:
- Modify/delete/rename any `.jsonl` file
- Modify `Profile.json`
- Create new cyborg folders
- Touch any file outside `/Aliens/Attendance/`

# [02] Inputs

## Targets (required)
- `ALL` — full rebuild
- `{CyborgID}` — single cyborg rebuild (repo dashboard also refreshed with latest counters)
- `{CID1,CID2,...}` — batch rebuild

## Description (required)
- Free-text reason (for log/audit)
- Empty => `ACC_ERR_DESCRIPTION_REQUIRED`

# [03] Manifest Gate
1) `/Aliens/manifest.json` must exist
2) `AllowShellCommands` — not required (pure FS read/write)
3) No network

# [04] Source-of-Truth Paths
- Ledger root:     `Attendance/Cyborg/{L1}/{L2}/{CID}/{YYYY}/{YYYY-MM-DD}.jsonl`
- Per-cyborg dash: `Attendance/Cyborg/{L1}/{L2}/{CID}/Dashboard.md`
- Repo dash:       `Attendance/Dashboard.md`
- Counters:        `Attendance/_index.json`
- Sharding rule:   L1 = CID[0:2], L2 = CID[0:4] right-padded with `_`

# [05] Discovery Pass
1) Glob `Attendance/Cyborg/**/*.jsonl`
2) Parse each line as JSON (skip malformed lines; count as `INTEGRITY_MALFORMED_LINE`)
3) Build in-memory index:
   - `events_by_cyborg[{CID}]` = [event, ...]
   - `events_by_date[{YYYY-MM-DD}]` = int
   - `events_by_hour_local[0..23]` = int
   - `events_by_weekday[0..6]` = int
   - `last_event_by_cyborg[{CID}]` = ISO ts
   - `first_event_by_cyborg[{CID}]` = ISO ts (for "new joiners")
   - `unmatched_checkins` = count of `checkin` without later same-day `checkout`

# [06] Per-Cyborg Dashboard Rebuild
For each `{CID}` in scope:
1) Compute per-cyborg aggregates:
   - total events, total checkin, total checkout
   - streak (consecutive days with at least 1 checkin)
   - last 7 / 30 day event counts
   - first-seen, last-seen
2) Render `Dashboard.md` template (see §8) and overwrite the file
3) Never delete the file if source JSONL is missing — render an "inactive" state instead

# [07] Repo Dashboard Rebuild
1) Compute global aggregates across all cyborgs
2) Render `/Aliens/Attendance/Dashboard.md` using template (see §9) and overwrite
3) Sections:
   - Daily 30d bar (mermaid xychart-beta)
   - Hourly 24 bar
   - Weekday 7 bar
   - Checkin/Checkout pie
   - Top-10 most-active cyborgs (last 30d)
   - New joiners (first-seen in last 7d)
   - Data-integrity panel (malformed lines, unmatched checkins, orphan folders)
   - Rebuild metadata footer (ts, duration ms, events scanned)

# [08] Per-Cyborg Dashboard Template (summary)
```markdown
# Attendance — {CID}

_Rebuilt at {ts_iso} by `Workflow("Attendance_Dashboard", ...)`._

## Snapshot
| Metric             | Value |
|--------------------|-------|
| Total events       | {n_events} |
| Total check-ins    | {n_checkin} |
| Total check-outs   | {n_checkout} |
| Current streak (d) | {streak} |
| First seen         | {first_iso} |
| Last seen          | {last_iso} |

## Last 7 days
```mermaid
xychart-beta
  title "Events / day (last 7d)"
  x-axis [...]
  y-axis "Events"
  bar [...]
```

## Last 30 days
```mermaid
xychart-beta
  title "Events / day (last 30d)"
  x-axis [...]
  y-axis "Events"
  bar [...]
```

## Integrity
| Check                        | Value |
|------------------------------|-------|
| Unmatched check-ins (30d)    | {n}   |
| Malformed JSONL lines        | {n}   |
```

# [09] Repo Dashboard Template (summary)
Same visual spec as existing `/Aliens/Attendance/Dashboard.md`, but **fully recomputed** (not incremental). Placeholders replaced deterministically. Any section whose data is empty renders `_(no data yet)_` instead of broken mermaid blocks.

# [10] _index.json Counters Block (optional)
Add / refresh keys under `Counters`:
```json
{
  "Counters": {
    "LastRebuildIso": "{ts}",
    "TotalEvents": N,
    "TotalCyborgs": N,
    "Top10LastRebuiltIso": "{ts}",
    "UnmatchedCheckins30d": N,
    "MalformedLines": N
  }
}
```
- Never delete other `_index.json` keys. Additive only.

# [11] Report
```
=== Attendance_Dashboard Report ===
Scope:       {ALL | CID1,CID2,...}
Reason:      {description}
Started:     {ts}
Finished:    {ts}
Duration:    {ms} ms

JSONL files scanned:   N
Events parsed:          N
Cyborgs touched:        N
Per-cyborg dashboards:  written N / skipped M
Repo dashboard:         written
_index.json counters:   updated

Integrity:
  Malformed lines:       N
  Unmatched check-ins:   N (30d window)
  Orphan folders:        N  (no .jsonl found)

Overall: OK | PARTIAL | FAIL
```

# [12] Error / Warning Codes
| Code | Meaning | Severity |
|---|---|---|
| `ACC_ERR_TARGETS_REQUIRED`            | Targets empty                   | HARD |
| `ACC_ERR_DESCRIPTION_REQUIRED`        | Description empty               | HARD |
| `DASH_ERR_INDEX_MISSING`              | `Attendance/_index.json` missing | HARD |
| `DASH_WARN_CID_NOT_FOUND`             | Requested cyborg has no folder  | WARN |
| `DASH_WARN_ORPHAN_FOLDER`             | Cyborg folder without any jsonl | WARN |
| `INTEGRITY_MALFORMED_LINE`            | JSONL line failed JSON.parse    | WARN |
| `INTEGRITY_UNMATCHED_CHECKIN`         | Check-in without Check-out      | INFO |
| `DASH_OK_REBUILT`                     | Dashboard rewritten successfully | OK   |

# [13] Recommended Usage
- **Check-in** invokes with `ALL | "Post-Check-in refresh"` (counters stay warm).
- **Check-out** invokes with `{CID} | "Post-checkout refresh"` (light path).
- Weekly cron/scheduled: `ALL | "Weekly full rebuild"`.

# [14] Done Criteria
- No JSONL file mutated
- No `Profile.json` mutated
- At least 1 dashboard file overwritten
- `_index.json` counters refreshed (if targets=ALL)
- Aggregate report generated
