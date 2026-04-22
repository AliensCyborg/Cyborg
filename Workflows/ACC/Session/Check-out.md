---
WorkflowId: Check-out
Type: Workflow_Plural
Domain: Session
Category: Session Lifecycle — Check-out Orchestrator
Status: Draft
Version: 26.04.01
LastUpdated: 2026-04-19
EntryPoint: true
Purpose: >
  Daily session-end orchestrator. Mirror of `Check-in`, but outbound:
    1) Record `checkout` attendance event (sharded, append-only)
    2) Push every team repo's `{CyborgID}` branch to remote
       (Cyborg + .github + WebOS intentionally **not** pushed)
    3) Generate a daily `Report` (what changed today, per repo)
    4) Refresh `Attendance_Dashboard` for this cyborg (incremental)
    5) Session-end UX + integrity tail-suite
  Child failures isolated; final aggregate report produced.
Invocation:
  Signature:
    - Workflow("Check-out", "C1071", "IST")
    - Workflow("Check-out", "{CyborgID}", "{TZ}")
  TargetsMeaning: >
    Cyborg / Developer ID (e.g. `C1071`). Exactly one.
    Empty/missing => `ACC_ERR_TARGETS_REQUIRED`.
  DescriptionMeaning: >
    Human timezone label (e.g. `IST`, `UTC`, `PST`). Required.
    Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.
ChildWorkflows:
  - Attendance
  - Git_Push
  - Report
  - Attendance_Dashboard
Safety:
  NoDelete: true
  NoForcePush: true
  NoRebase: true
  NoResetHard: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
---

# Check-out

# [00] Philosophy (NON-NEGOTIABLE)
```
DELEGATE. ISOLATE. REPORT. PER-CYBORG BRANCH. OUTBOUND ONLY.
```
- Check-out is a pure orchestrator — no inline git work beyond pre-flight.
- Outbound mirror of Check-in: **push, don't pull**.
- Per-cyborg isolation: only `{CyborgID}` branches are pushed. Cyborg + .github
  stay on `main` and are **never pushed** from a cyborg workstation.
- Child failures isolated: one repo failing does not stop the rest.

# [01] Strict Scope Lock
Allowed:
- Invoke `Attendance` once (`checkout` event)
- Invoke `Git_Push` per team repo (on `{CyborgID}` branch)
- Invoke `Report` once (daily session report)
- Invoke `Attendance_Dashboard` once for this cyborg (incremental refresh)
- Read-only UX outputs (day summary, tomorrow hints, unresolved items)
- Read-only integrity checks (uncommitted changes, unpushed commits, pending CSV)

Forbidden:
- Pushing `Cyborg`, `.github`, `WebOS` (permanently banned)
- Force-push anywhere
- `git rebase`, `git reset --hard`, `git push --force`, `git push --mirror`
- Auto-commit on behalf of the user (Check-out never writes commits)
- Clone / pull (Check-in's job)
- Auto-resolve merge conflicts

# [02] Inputs

## Targets
- Exactly one Cyborg ID (e.g. `C1071`)
- Must match manifest `Developer.CyborgID` (integrity gate)
- Empty/missing => `ACC_ERR_TARGETS_REQUIRED`

## Description
- Timezone label (e.g. `IST`). Required.
- Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`

# [03] Manifest Gate
1) Read `/Aliens/manifest.json`
2) Verify `AllowShellCommands: true` (Git_Push needs git CLI)
3) `NoDelete: true` enforce
4) Identity snapshot:
   - `developer_username` = `Developer.Username`
   - `cyborg_id`          = Targets
   - `tz_label`           = Description
   - `github_account`     = `Developer.GitAccount` if present, else
                            `git config --get user.email`, else empty
5) Target vs manifest CyborgID mismatch => `CHECKOUT_ERR_IDENTITY_MISMATCH`.

# [04] Repo Model

## 4.1 Never-push group (hard ban)
```
Cyborg     # platform rules/workflows — push only via release process
.github    # Copilot instructions — push only via release process
WebOS      # platform owner only
```
Any attempt to push these => hard-ignore + `CHECKOUT_ERR_PROTECTED_REPO`.

## 4.2 Push group (branch = `{CyborgID}`)
```
.Alien, ACLE, Attendance, Blog, Course, Course_ACLE, Course_ATLE,
Docs, Feedback, Project, Report, Skills, Skill_ACLE, Skill_ATLE,
WebApp, WebBrand, WebDB, WebSDK, Wiki, Wiki_ACLE, Wiki_ATLE
```
(Total: 21 repos — same list as Check-in §4.2. Kept in SSOT sync.)

Rules (per repo):
- If `/Aliens/{RepoName}/.git/` missing → skip silently.
- If present but branch `{CyborgID}` missing (local + remote) → skip with
  `CHECKOUT_WARN_BRANCH_MISSING` (suggest `Workflow("Git_Branch", ...)`).
- If current branch != `{CyborgID}` → report `CHECKOUT_WARN_WRONG_BRANCH`
  (do NOT auto-switch; user may be mid-task on another branch intentionally).
- If working tree dirty OR there are unstaged/uncommitted changes →
  `CHECKOUT_WARN_DIRTY` (do NOT auto-commit; surface and continue).
- If nothing to push (upstream up-to-date) → `PUSH_NOTHING` (OK).
- Else invoke `Git_Push` with contract: no force, no mirror, no delete.

# [05] Execution Plan (high level)

| # | Step                                              | Child                 | Failure |
|---|---------------------------------------------------|-----------------------|---------|
| 1 | Pre-flight scan + integrity snapshot              | inline                | SOFT    |
| 2 | Record checkout event                             | Attendance            | SOFT*   |
| 3 | Session duration sanity                           | inline                | SOFT    |
| 4 | Push team repos on `{CyborgID}`                   | Git_Push (per repo)   | SOFT**  |
| 5 | Generate daily session Report                     | Report                | SOFT    |
| 6 | Refresh Attendance dashboard (this cyborg)        | Attendance_Dashboard  | SOFT    |
| 7 | Session-end UX + tail-suite                       | inline                | SOFT    |
| 8 | Aggregate report                                  | —                     | —       |

`*`  Attendance event append is SOFT locally, but the subsequent **Attendance repo push** in Step 4 is upgraded to **HARD** — the checkout event must reach GitHub for the ledger to be durable.
`**` Per-repo push failures are SOFT for the other 20 repos; only Attendance push is HARD.

# [06] Step 1 — Pre-flight scan

For each repo in §4.2, capture:
- On disk?                              → bool
- Current branch                        → str
- Branch == `{CyborgID}`?               → bool
- Uncommitted changes (porcelain)?      → bool
- Uncommitted file list (trimmed, first 20 per repo, with per-file status M/A/D/??)
- Unpushed commits (ahead of upstream)? → int
- Upstream set?                         → bool
- Today's LOC delta on `{CyborgID}`     → `+added / -removed across F files`
  (Computed once per repo: `git diff --shortstat {merge_base_with_main}..HEAD`)
- Last-merged-to-main age (days)        → int (for stale-branch warning)
- Merge-base with main OK?              → bool

## 6.1 Global pre-flight checks (not per-repo)

### 6.1.1 Git credential liveness
- On `.Alien` repo (always present, small): `git ls-remote --heads origin` dry-run.
- Fail → `CHECKOUT_WARN_GIT_CREDENTIALS` (warn user now; push in Step 4 likely to fail).
- Pass → log `github_account` used (informational).

### 6.1.2 Unmatched-checkin detector
- Scan `Attendance/Cyborg/{L1}/{L2}/{CID}/{YYYY}/*.jsonl` for the last **7 days**.
- For each day, pair checkin ↔ checkout events in chronological order.
- Any day where `checkin` events exceed `checkout` events →
  `CHECKOUT_WARN_UNMATCHED_CHECKIN` with the offending date list.
- History is **immutable**: we only warn, never back-fill. User may manually
  append a corrective `checkout` event via `Workflow("Attendance", ...)`
  with an explicit dated description if they choose.

### 6.1.3 Timezone drift
- Compare Description `{tz_label}` with the last event's `tz` in this cyborg's
  Profile.json.
- Mismatch → `CHECKOUT_WARN_TZ_DRIFT` (informational, e.g. user travelled).
- Also compare with manifest `Developer.Timezone` if present.

This snapshot powers Steps 3, 4, 5, and the end-of-day report.
Integrity warnings surface here (early), not during the push loop.

# [07] Step 2 — Record `checkout` event
```
Workflow("Attendance", "{CyborgID}", "checkout | {tz_label} | Check-out orchestrator")
```
- Local append is SOFT (a failed local record never blocks push/report).
- Durability is enforced in Step 4 Attendance-push (HARD).
- `Attendance` workflow updates Profile.json `last_event=checkout`, increments
  `totals.checkout`, updates `streaks`, touches per-cyborg Dashboard.

# [08] Step 3 — Session duration sanity

From the just-appended `checkout` event and the matching prior `checkin`:

- `duration_min = checkout_ts − checkin_ts`  (same day, same cyborg)
- If no matching `checkin` today  → `CHECKOUT_WARN_NO_CHECKIN_TODAY`
- If `duration_min < 5`            → `CHECKOUT_WARN_SUSPICIOUS_DURATION` (accidental double-checkout?)
- If `duration_min > 16 * 60`      → `CHECKOUT_WARN_SUSPICIOUS_DURATION` (forgot previous checkout?)
- Else                              → OK, record `duration_hhmm` for banner.

Never auto-correct. Only report.

# [09] Step 4 — Push team repos

For each repo in §4.2 where pre-flight says push is safe:
```
Workflow("Git_Push", "{RepoName}", "Check-out: daily outbound sync on branch {CyborgID}")
```
- Sequential (no parallel push — same policy as Git_Pull).
- `Git_Push` contract: no force, no mirror, no delete-remote, no tags push
  unless explicitly requested. Push only the current branch's HEAD.
- Pushable only when: on `{CyborgID}` branch, no merge-conflict, upstream set
  (or `-u` adds it the first time).
- Failures per repo are logged with code:
  - `PUSH_SUCCESS`         — pushed N commits
  - `PUSH_NOTHING`         — already up-to-date
  - `PUSH_WARN_NON_FF`     — non-fast-forward (user must pull first; never force)
  - `PUSH_ERR_NETWORK`     — fetch/push network failure
  - `PUSH_ERR_AUTH`        — credential failure
  - `PUSH_ERR_REMOTE`      — remote rejected (branch protection / hooks)

## 9.1 Attendance repo — HARD push (data durability critical)
- Attendance repo is pushed **first** in the loop.
- If Attendance push fails with any `PUSH_ERR_*` code:
  → `CHECKOUT_ERR_ATTENDANCE_PUSH_FAILED` (HARD).
  The checkout event exists only locally; durability is not satisfied.
  User is told explicitly: _"Fix network/auth, then re-run Check-out—
  Attendance will de-duplicate the local event on next append via the
  Attendance workflow's event_id contract."_
- All other 20 repos remain SOFT regardless of outcome.

# [09] Step 4 — Generate daily `Report`
```
Workflow("Report", "{CyborgID}", "Daily session report | {tz_label} | {YYYY-MM-DD}")
```
- The `Report` workflow already produces Planning / Code / Documentation
  sub-reports. For Check-out we ask for a **session-summary** variant that
  aggregates:
  - Today's commits per repo (from pre-flight + git log since last check-in)
  - Today's pushed commits (from Step 3 output)
  - Today's attendance events (checkin + checkout from today's JSONL)
  - Pending planner rows (not auto-started)
  - Unresolved integrity warnings from Step 1
- Report output location follows `Report` workflow's own SSOT.

# [10] Step 5 — Refresh Attendance dashboard (incremental)
```
Workflow("Attendance_Dashboard", "{CyborgID}", "Post-checkout refresh")
```
- Single-cyborg path (cheap). No full scan.
- Recomputes per-cyborg Dashboard.md from this cyborg's JSONL files.
- Repo Dashboard.md NOT rebuilt here (that stays for the scheduled `ALL` run).

# [11] Step 6 — Session-end UX + tail-suite

## U1. Day-end banner
```
╔══════════════════════════════════════════════════════╗
║  Good night, {display or CyborgID}                   ║
║  Session:   {checkin_iso} → {checkout_iso}           ║
║  Duration:  {hh:mm}                                  ║
║  Commits:   {n} across {k} repos                     ║
║  Pushed:    {n} | Dirty repos: {d}                   ║
╚══════════════════════════════════════════════════════╝
```

## U2. Unresolved items (printed, never auto-fixed)
- Repos with uncommitted changes
- Repos with unpushed commits that failed push
- Branches not matching `{CyborgID}` (switched mid-day?)
- Pending planner CSV rows (`status=0`)
- Unmatched check-ins (check-in today but no check-out yet — corrected by §7)

## U3. Tomorrow hint
- Next likely planner CSV row
- Any open Code/Plan/Docs tasks from today's Report
- VS Code recommended extensions unmet

## U4. Integrity tail-suite (soft, read-only)
- Disk space under `/Aliens/` < 5 GB → `CHECKOUT_WARN_DISK_LOW`
- Log rotation check (Runs/ older than 30d) → `CHECKOUT_WARN_LOG_ROT`
- Secret file health (read-only sanity) → `CHECKOUT_WARN_SECRETS`
- Clock drift vs NTP              → `CHECKOUT_WARN_CLOCK_DRIFT`

# [12] Error / Warning Codes

| Code | Meaning | Severity |
|---|---|---|
| `ACC_ERR_TARGETS_REQUIRED`            | Target CyborgID missing                   | HARD |
| `ACC_ERR_DESCRIPTION_REQUIRED`        | TZ label missing                          | HARD |
| `CHECKOUT_ERR_IDENTITY_MISMATCH`      | Target != manifest.Developer.CyborgID     | HARD |
| `CHECKOUT_ERR_ATTENDANCE_REPO_MISSING`| `/Aliens/Attendance/.git/` missing        | HARD |
| `CHECKOUT_ERR_PROTECTED_REPO`         | Attempt to push Cyborg/.github/WebOS      | HARD |
| `CHECKOUT_WARN_BRANCH_MISSING`        | `{CyborgID}` branch absent on repo        | WARN |
| `CHECKOUT_WARN_WRONG_BRANCH`          | Current branch != `{CyborgID}`            | WARN |
| `CHECKOUT_WARN_DIRTY`                 | Uncommitted/untracked changes             | WARN |
| `CHECKOUT_WARN_UNPUSHED`              | Local ahead of upstream after push        | WARN |
| `CHECKOUT_WARN_DISK_LOW`              | < 5 GB free under `/Aliens/`              | WARN |
| `CHECKOUT_WARN_LOG_ROT`               | Runs/ older than 30d                      | INFO |
| `CHECKOUT_WARN_SECRETS`               | Secret health anomaly                     | WARN |
| `CHECKOUT_WARN_CLOCK_DRIFT`           | Drift > 60 s                              | WARN |
| `CHECKOUT_WARN_ATTENDANCE_FAILED`     | Attendance record soft-failed             | WARN |
| `CHECKOUT_WARN_REPORT_FAILED`         | Report workflow soft-failed               | WARN |
| `CHECKOUT_WARN_DASHBOARD_FAILED`      | Attendance_Dashboard soft-failed          | WARN |

# [14] Aggregate Report (final)

```
=== Check-out Report ===
Cyborg:      {CyborgID}
TZ:          {tz_label}
Started:     {ts_start_iso}
Finished:    {ts_end_iso}
Duration:    {ms} ms

--- Push Status (21 repos) ---
| Repo        | On disk | Branch      | Ahead | Status            |
|-------------|---------|-------------|-------|-------------------|
| .Alien      |   yes   | C1071       | 3     | PUSH_SUCCESS      |
| ACLE        |   yes   | C1071       | 0     | PUSH_NOTHING      |
| Docs        |   yes   | main        | 7     | CHECKOUT_WARN_WRONG_BRANCH |
| Feedback    |    no   | —           | —     | skipped           |
...
Pushed:      N repos
Nothing:     M repos
Skipped:     K repos
Failed:      F repos

--- Attendance ---
Event:       checkout | {tz_label}
Ledger:      Attendance/Cyborg/{L1}/{L2}/{CID}/{YYYY}/{YYYY-MM-DD}.jsonl
Profile:     updated (last_event=checkout)

--- Daily Report ---
Location:    {Report workflow output path}
Commits:     N across K repos
Events:      checkin=1, checkout=1
Pending CSV: P rows (not auto-started)

--- Dashboard ---
Per-cyborg Dashboard.md: refreshed
Repo Dashboard.md:       (not rebuilt — run `Attendance_Dashboard("ALL", ...)` weekly)

--- Integrity tail ---
Disk:        {free_gb} GB free
Clock:       drift {ms} ms
Secrets:     OK
Log rot:     {N files rotated}

Overall: OK | PARTIAL | FAIL
```

# [15] Permanently Forbidden
- Pushing `Cyborg`, `.github`, `WebOS`
- `git push --force`, `--force-with-lease`, `--mirror`, `--delete`
- `git rebase`, `git reset --hard`, `git branch -D`
- Auto-committing on behalf of user
- Cloning any repo
- Touching anyone else's JSONL history
- Back-filling past-day attendance events silently (history is immutable)

# [16] Done Criteria
- Pre-flight snapshot produced (incl. credentials, unmatched-checkin, TZ drift)
- `checkout` attendance event appended (or soft-failed with explicit warning)
- Attendance repo push succeeded (HARD) — or run marked FAIL
- Other team repos push attempted individually (success/skip/fail each logged)
- Daily Report generated
- Per-cyborg Dashboard refreshed
- Handoff note captured (if user provided)
- VS Code state snapshot attempted (best-effort)
- Aggregate Check-out report printed
