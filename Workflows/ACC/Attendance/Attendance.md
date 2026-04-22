---
WorkflowId: Attendance
Type: Workflow_Utility
Domain: Attendance
Category: Attendance — Append Event + Regenerate Derived Caches
Status: Draft
Version: 26.04.03
LastUpdated: 2026-04-19
EntryPoint: true
Purpose: >
  Cyborg attendance event ko safely append karna aur derived caches
  (Profile.json + per-cyborg Dashboard.md + repo-level Dashboard.md + _index.json)
  ko atomically refresh karna. Storage 3-level sharded hai taki ek bhi folder
  1000 files se upar na jaye (GitHub practical limit).
  Append-only. History immutable.
Invocation:
  Signature:
    - Workflow("Attendance", "C1071", "checkin | IST")
    - Workflow("Attendance", "C1071", "checkout | IST")
    - Workflow("Attendance", "C1071", "{type} | {tz} | {free-text}")
  TargetsMeaning: >
    Cyborg / Developer ID (e.g. `C1071`). Exactly one per invocation.
  DescriptionMeaning: >
    Pipe-separated: `{type} | {tz} | {context?}`
      type    ∈ {checkin, checkout, handoff} (required)
      tz      = IST | UTC | PST | ...         (required)
      context = free text                     (optional)
    Missing/empty => `ACC_ERR_DESCRIPTION_REQUIRED`.
Safety:
  NoDelete: true
  AppendOnly: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: false
---

# Attendance

# [00] Philosophy (NON-NEGOTIABLE)
```
APPEND-ONLY LEDGER. DERIVED CACHES. SHARDED AT THE TOP.
```
- `Cyborg/{L1}/{L2}/{CyborgID}/{YYYY}/{YYYY-MM-DD}.jsonl` files are
  **append-only**. Zero edits, zero deletes.
- `Profile.json` + `Dashboard.md` (both repo-level and per-cyborg) are
  **derived caches**. Rebuildable from the JSONL ledger.
- Only the top-level `Cyborg/` folder is prefix-sharded (L1/L2) so no
  directory ever exceeds ~1000 children at any scale (1M–10M cyborgs).
- Inside each cyborg, the layout is simple: `{YYYY}/{YYYY-MM-DD}.jsonl`
  — 366 files/year, always under the cap.

# [01] Strict Scope Lock
Allowed:
- Append JSONL event line to today's per-cyborg dated file
- Atomic write: per-cyborg `Profile.json`
- Atomic write: per-cyborg `Dashboard.md`
- Atomic write: repo-level `Dashboard.md` (incremental refresh)
- Atomic write: repo-level `_index.json`

Forbidden:
- Edit/delete any existing JSONL line or file
- Write outside `/Aliens/Attendance/`
- Clone repos, push branches (scope = local writes only)
- Legacy paths (`Records/...`, flat `Cyborgs/{id}.json`) — forbidden in v1.2.0+

# [02] Inputs

## Targets
- Exactly one Cyborg ID (e.g. `C1071`)
- Must match regex `^[A-Za-z][A-Za-z0-9_]{0,31}$`
- Invalid => `ATT_ERR_CYBORG_ID_INVALID`

## Description (`{type} | {tz} | {context?}`)
- `type` required ∈ {`checkin`, `checkout`} (case-insensitive → lowercase)
- `tz`   required non-empty
- `context` optional
- Invalid/missing => `ACC_ERR_DESCRIPTION_REQUIRED` or `ATT_ERR_EVENT_TYPE_INVALID`

# [03] Manifest + Repo Gate

## 3.1 Manifest
- Read `/Aliens/manifest.json`
- Need: `AllowCreate: true`, `AllowModify: true`, `NoDelete: true`

## 3.2 Attendance repo presence
- `/Aliens/Attendance/` must exist AND have `.git/`
- Missing => `ATT_ERR_REPO_MISSING` (do NOT auto-init here; that's Check-in's call)

## 3.3 Identity resolution (for event payload)
- `developer_username` = manifest `Developer.Username` (e.g. `AnilNayak`)
- `github_account`     = manifest `Developer.GitAccount` if present, else
                         `git config --get user.email` (local), else empty
- `agent_name`         = current agent name (e.g. `AnilCyborg`)
- **Do NOT assume** the local git account is the canonical one — different
  cyborgs may log in with different GitHub accounts on different machines.

## 3.4 Timezone resolution
| Label | Offset   |
|-------|----------|
| IST   | +05:30   |
| UTC   | +00:00   |
| GMT   | +00:00   |
| PST   | -08:00   |
| EST   | -05:00   |
| CET   | +01:00   |
| JST   | +09:00   |
| AEST  | +10:00   |

- Unknown label allowed — store as-is, offset defaults to `+00:00`,
  warning `ATT_WARN_UNKNOWN_TZ`.

# [04] Shard Path Computation

```
def shard(cyborg_id):
    L1 = cyborg_id[0:2]
    L2 = cyborg_id[0:4].ljust(4, "_")
    return (L1, L2)

def cyborg_root(cyborg_id):
    L1, L2 = shard(cyborg_id)
    return f"Cyborg/{L1}/{L2}/{cyborg_id}"

def daily_jsonl(cyborg_id, date_key):
    year = date_key[:4]
    return f"{cyborg_root(cyborg_id)}/{year}/{date_key}.jsonl"
```

| CyborgID | Cyborg root              | Daily JSONL                                    |
|----------|--------------------------|------------------------------------------------|
| `C1071`  | `Cyborg/C1/C107/C1071/`  | `Cyborg/C1/C107/C1071/2026/2026-04-19.jsonl`   |
| `C9`     | `Cyborg/C9/C9__/C9/`     | `Cyborg/C9/C9__/C9/2026/2026-04-19.jsonl`      |
| `C12345` | `Cyborg/C1/C123/C12345/` | `Cyborg/C1/C123/C12345/2026/2026-04-19.jsonl`  |

# [05] Build the Event

## 5.1 Timestamps
- `now_utc`   = `datetime.now(timezone.utc)`
- `ts_iso`    = `now_utc` converted to resolved tz offset, ISO 8601
- `date_key`  = `YYYY-MM-DD` (local date)
- `year`, `month` = components of `date_key`

## 5.2 Host + commit
- `host`   = machine hostname (best-effort; empty on error)
- `commit` = `git -C /Aliens/Cyborg rev-parse --short HEAD` (best-effort; empty on error)

## 5.3 Event JSON (single-line when serialized)
```json
{
  "type":    "checkin",
  "cyborg":  "C1071",
  "agent":   "AnilCyborg",
  "ts_iso":  "2026-04-19T10:30:15+05:30",
  "tz":      "IST",
  "host":    "DESKTOP-XYZ",
  "commit":  "abc1234",
  "context": "optional free text or empty"
}
```

# [06] Append to the per-day ledger file

## 6.1 Path
```
/Aliens/Attendance/Cyborg/{L1}/{L2}/{CyborgID}/{YYYY}/{YYYY-MM-DD}.jsonl
```

## 6.2 Atomic append
- `mkdir -p` all intermediate folders.
- Serialize event to single-line JSON (no embedded newlines).
- Open in append mode, UTF-8.
- Write `{json_line}\n`; flush; close.
- Verify file grew by `len(json_line)+1` bytes.
  - Mismatch => retry once → `ATT_ERR_APPEND_FAILED`.

## 6.3 Duplicate guard
- If last line has same `type` + `cyborg` and `ts_iso` delta <= 5s:
  - Skip; return `ATT_WARN_DUPLICATE_EVENT`.

# [07] Update per-cyborg `Profile.json`

## 7.1 Path
```
/Aliens/Attendance/Cyborg/{L1}/{L2}/{CyborgID}/Profile.json
```

## 7.2 Load / init
Missing => initialize:
```json
{
  "cyborg_id":      "{CyborgID}",
  "display":        "{developer_username}",
  "github_account": "{github_account}",
  "tz":             "{tz}",
  "first_seen":     "{ts_iso}",
  "last_seen":      "{ts_iso}",
  "last_event":     "{type}",
  "totals":         {"checkins":0, "checkouts":0, "days_active":0, "total_hours":0.0},
  "streaks":        {"current_days":0, "longest_days":0, "longest_end":null},
  "recent_events":  [],
  "updated":        "{ts_iso}"
}
```
Invalid JSON => backup as `.corrupt-{epoch}` → re-init → warn `ATT_WARN_PROFILE_CORRUPT`.

## 7.3 Field updates
- `last_seen`  = `ts_iso`
- `last_event` = `type`
- `tz`         = `tz` (latest wins)
- `updated`    = `ts_iso`
- `totals.checkins`  += 1 if `type=="checkin"`  else unchanged
- `totals.checkouts` += 1 if `type=="checkout"` else unchanged
- `totals.days_active`: if `date_key` != date of previous `last_seen`: += 1
- `totals.total_hours`:
  - On `checkout` with matching same-day `checkin` in `recent_events`:
    += `(ts_checkout - ts_checkin)` in hours (2 dp)
  - Otherwise unchanged
- `streaks`:
  - `prev_date` = date of previous `last_seen`
  - Same day        → unchanged
  - Next day        → `current_days += 1`
  - Else            → `current_days = 1`
  - If `current_days > longest_days`: update `longest_days`, `longest_end = date_key`
- `recent_events`: prepend event; keep first 10.

## 7.4 Atomic write
- `{path}.tmp` → `os.replace(tmp, final)` — UTF-8, `indent=2`.

# [08] Regenerate per-cyborg `Dashboard.md`

## 8.1 Path
```
/Aliens/Attendance/Cyborg/{L1}/{L2}/{CyborgID}/Dashboard.md
```

## 8.2 Data collection (for this cyborg)
- `profile` = freshly-updated `Profile.json`
- Scan `Cyborg/{L1}/{L2}/{CyborgID}/**/*.jsonl` (only this cyborg's ledger)
- Build:
  - `events_30d[date_key]` count (trailing 30 days)
  - `hour_hist[0..23]` for check-ins over trailing 90 days
  - `weekday_hist[0..6]` (Mon..Sun) over trailing 90 days

## 8.3 Template (fill in)
```markdown
# Cyborg Dashboard — {cyborg_id}

| Field | Value |
|---|---|
| CyborgID       | `{cyborg_id}` |
| Display        | `{display}` |
| GitHub account | `{github_account}` |
| Timezone       | `{tz}` |
| First seen     | `{first_seen}` |
| Last seen      | `{last_seen}` |
| Last event     | `{last_event}` |
| Updated        | `{updated}` |

## 1. KPIs

| Metric | Value |
|---|---|
| Check-ins       | `{totals.checkins}` |
| Check-outs      | `{totals.checkouts}` |
| Days Active     | `{totals.days_active}` |
| Total Hours     | `{totals.total_hours}` |
| Current Streak  | `{streaks.current_days}` days |
| Longest Streak  | `{streaks.longest_days}` days (ended `{streaks.longest_end}`) |

## 2. Daily Events (last 30 days)

Rendered via Mermaid `xychart-beta` with x=dates, y=events/day.

## 3. Check-in Hour Distribution ({tz}, last 90 days)

Rendered via Mermaid `xychart-beta` with 24 hourly buckets.

## 4. Weekday Distribution (last 90 days)

Rendered via Mermaid `xychart-beta` — Mon..Sun bars.

## 5. Event Type Split

Rendered via Mermaid `pie` — Check-in vs Check-out.

## 6. Recent Events (last 10)

| Time ({tz}) | Type | Host | Commit | Context |
|-------------|------|------|--------|---------|
{recent_events_table}

---
_Auto-generated by `Workflow("Attendance", ...)`. Do not edit by hand._
```

Note: in the actual rendered file the three `mermaid` code blocks are fully
populated — the template above is abbreviated for SSOT readability. See the
repo-level `Dashboard.md` at `/Aliens/Attendance/Dashboard.md` for the
full `xychart-beta` + `pie` syntax to mirror.

## 8.4 Atomic write
- `{path}.tmp` → `os.replace(tmp, final)` — UTF-8.

# [09] Update repo-level `Dashboard.md`

Incremental refresh (cheap path):
- `Global KPIs` — bump totals counters, `last_event_iso`, active-today/week/month
  (using quick scan of `Cyborgs/**/Profile.json` `last_seen`).
- `Recent Events (last 20)` — prepend new event; keep first 20.
- `Daily Event Volume (last 30 days)` — today's bar += 1 (or rebuild if date advanced).
- Other sections (Top 10, New Cyborgs, Integrity) — left stale; refreshed by
  `Dashboard_Rebuild` workflow (future) on schedule / on demand.

Atomic tmp+replace.

# [10] Update repo-level `_index.json`

- `totals.events`        += 1
- `totals.checkins` / `totals.checkouts` += 1 as appropriate
- `totals.last_event_iso` = `ts_iso`
- `totals.cyborgs`       = **recount** `Cyborg/**/Profile.json` glob
- `totals.days_active`   = **recount** distinct date keys from all
                           `Cyborg/**/*.jsonl` filenames (stem = `YYYY-MM-DD`)
- `updated`              = `ts_iso`

Atomic tmp+replace.

# [11] Output

```
=== Attendance ===
Cyborg:      {CyborgID}  (shard {L1}/{L2})
Event:       {type}
TS ({tz}):   {ts_iso}
Host:        {host}
Commit:      {commit}

Ledger:      Cyborg/{L1}/{L2}/{CyborgID}/{YYYY}/{date_key}.jsonl          (appended)
Profile:     Cyborg/{L1}/{L2}/{CyborgID}/Profile.json                    (updated)
CyDashboard: Cyborg/{L1}/{L2}/{CyborgID}/Dashboard.md                    (regenerated)
RepoDash:    Dashboard.md                                                 (incremental refresh)
Index:       _index.json                                                  (totals.events = N)
Status:      OK
```

# [12] Error / Warning Codes

| Code | Meaning | Action |
|---|---|---|
| `ACC_ERR_TARGETS_REQUIRED`     | Cyborg ID missing              | Fail fast |
| `ACC_ERR_DESCRIPTION_REQUIRED` | Description empty              | Fail fast |
| `ATT_ERR_CYBORG_ID_INVALID`    | ID fails regex                 | Fail fast |
| `ATT_ERR_EVENT_TYPE_INVALID`   | type not in {checkin,checkout} | Fail fast |
| `ATT_ERR_REPO_MISSING`         | /Aliens/Attendance absent      | Fail fast |
| `ATT_ERR_APPEND_FAILED`        | JSONL append verify failed     | Retry once then fail |
| `ATT_WARN_UNKNOWN_TZ`          | Unknown tz label               | Continue, log |
| `ATT_WARN_DUPLICATE_EVENT`     | Duplicate within 5s            | Skip append |
| `ATT_WARN_PROFILE_CORRUPT`     | Profile.json invalid           | Backup + re-init |

# [13] Permanently Forbidden
- Editing existing JSONL lines / files
- Deleting any file under `Cyborg/`
- Writing under legacy paths (`Records/...`, flat `Cyborgs/{id}.json`)
- Writing outside `/Aliens/Attendance/`
- `git reset --hard` / `git clean -fd` on this repo

# [14] Done Criteria
- Event appended atomically (sharded path)
- `Profile.json` updated atomically
- Per-cyborg `Dashboard.md` regenerated (with populated mermaid charts)
- Repo-level `Dashboard.md` incrementally refreshed
- `_index.json` totals refreshed
- No historical data touched
- Status report printed
