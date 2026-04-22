---
WorkflowId: Check-in
Type: Workflow_Plural
Domain: Session
Category: Session Lifecycle — Check-in Orchestrator
Status: Draft
Version: 26.04.02
LastUpdated: 2026-04-19
EntryPoint: true
Purpose: >
  Daily session-start orchestrator for any Aliens cyborg/developer.
  Runs a deterministic sequence of child workflows + UX + integrity checks:
    1) Critical repo pulls on `main`      (Cyborg, .github)
    2) Team repo pulls on `{CyborgID}`    (existing-only, per-cyborg branch)
    3) Attendance event record             (type=checkin, sharded)
    4) Session-start UX + integrity suite  (10 tasks)
  Child failures are isolated; final aggregate report is produced.
Invocation:
  Signature:
    - Workflow("Check-in", "C1071", "IST")
    - Workflow("Check-in", "{CyborgID}", "{TZ}")
  TargetsMeaning: >
    Cyborg / Developer ID (e.g. `C1071`). Exactly one.
    Empty/missing => `ACC_ERR_TARGETS_REQUIRED`.
  DescriptionMeaning: >
    Human timezone label (e.g. `IST`, `UTC`, `PST`). Required.
    Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`.
ChildWorkflows:
  - Git_Pull
  - Attendance
Safety:
  NoDelete: true
  NoForcePush: true
  NoRebase: true
  NoResetHard: true
  ProdWrite: false
  DbTouch: "not-required"
  InternetRequired: true
---

# Check-in

# [00] Philosophy (NON-NEGOTIABLE)
```
DELEGATE. ISOLATE. REPORT. PER-CYBORG BRANCH.
```
- Check-in sirf orchestrator hai — khud kuch lines of actual work nahi karta.
- Saari heavy-lift child workflows karti hain (`Git_Pull`, `Attendance`).
- Har cyborg apni **own branch** (`{CyborgID}`) par kaam karta hai; sirf
  `Cyborg` + `.github` repos exception hain (always `main`).
- Child failures isolated: ek step fail => dusre run hotay rehnge.

# [01] Strict Scope Lock
Allowed:
- Invoke `Git_Pull` per repo (with branch checkout preamble)
- Invoke `Attendance` once (checkin event)
- Read-only UX outputs (banner, CSV list, role reminder, digest)
- Read-only integrity checks (secret health, clock drift, manifest validation,
  branch sanity, disk space)
- Log rotation under `/Aliens/.Alien/{dev}/Runs/` (archive-only, no delete of live data)

Forbidden:
- Clone any repo (even if missing locally — user decision, not ours)
- Create files outside what child workflows create / UX outputs require
- Auto-start any planner CSV row
- Parallel `Git_Pull` (current policy = sequential for safety)
- Modify the repo list at runtime based on guesswork

# [02] Inputs

## Targets
- Exactly one Cyborg ID (e.g. `C1071`)
- Empty/missing => `ACC_ERR_TARGETS_REQUIRED`

## Description
- Timezone label (e.g. `IST`). Required.
- Empty/missing => `ACC_ERR_DESCRIPTION_REQUIRED`

# [03] Manifest Gate
1) Read `/Aliens/manifest.json`
2) Verify `AllowShellCommands: true` (Git_Pull needs git CLI)
3) `NoDelete: true` enforce
4) Identity snapshot:
   - `developer_username` = `Developer.Username`
   - `cyborg_id`          = Targets (e.g. `C1071`)
   - `tz_label`           = Description
   - `github_account`     = `Developer.GitAccount` if present, else
                            `git config --get user.email`, else empty
5) **GitHub account is NOT assumed to be any specific account.** Each cyborg
   may have different credentials on different machines. Workflow uses whatever
   is configured locally (SSH key / PAT / credential manager).

# [04] Repo Model

## 4.1 Critical group (branch = `main`)
```
Cyborg     # latest Aliens rules + workflows
.github    # latest Copilot instructions
```
- Both MUST be present; both MUST be on `main` after checkout step.
- Missing folder or non-`main` branch that cannot be checked out
  => `CHECKIN_ERR_CRITICAL_REPO`.

## 4.2 Team group (branch = `{CyborgID}`)
```
.Alien
ACLE
Attendance
Blog
Course
Course_ACLE
Course_ATLE
Docs
Feedback
Project
Report
Skills
Skill_ACLE
Skill_ATLE
WebApp
WebBrand
WebDB
WebSDK
Wiki
Wiki_ACLE
Wiki_ATLE
```
(Total: 21 repos. **WebOS intentionally excluded** — only the platform owner
works on it.)

Rules:
- For each: if `/Aliens/{RepoName}/.git/` missing → **skip silently** (no clone).
- If present: ensure branch `{CyborgID}` is checked out, then pull.
- If `{CyborgID}` branch missing locally AND missing on remote →
  skip with `CHECKIN_WARN_BRANCH_MISSING` (user must bootstrap the branch
  for this cyborg on that repo first; Check-in never auto-creates it).
- If `{CyborgID}` branch missing locally but exists on remote →
  `git fetch` + `git checkout -b {CyborgID} origin/{CyborgID}` (allowed).
- Underscore naming is canonical (`Wiki_ACLE`, not `Wiki ACLE`).

# [05] Execution Plan (high level)

| # | Step                                              | Child              | Failure |
|---|---------------------------------------------------|--------------------|---------|
| 1 | Pull `Cyborg` (main)                              | Git_Pull           | HARD    |
| 2 | Pull `.github` (main)                             | Git_Pull           | HARD    |
| 3 | Branch prep + pull team repos on `{CyborgID}`     | Git_Pull (batch)   | SOFT    |
| 4 | Verify Attendance repo + record checkin event     | Attendance  | SOFT    |
| 5 | Session-start UX + integrity suite (10 tasks)     | inline             | SOFT    |
| 6 | Aggregate report                                  | —                  | —       |

# [06] Step 1–2: Critical repo pulls (main)

For each of `Cyborg` and `.github`:

1. Confirm `/Aliens/{Repo}/.git/` exists. Missing => HARD FAIL.
2. Ensure current branch == `main`:
   - If current != `main`:
     - If tree dirty: auto-stash (standard Git_Pull stash contract).
     - `git -C /Aliens/{Repo} checkout main`
     - If checkout fails: HARD FAIL `CHECKIN_ERR_CRITICAL_REPO`
3. Invoke:
   ```
   Workflow("Git_Pull", "{Repo}", "Check-in: refresh {Repo} on main")
   ```
4. Status ∉ {`PULL_SUCCESS`, `PULL_NOTHING`} => HARD FAIL.

Note: Pulled Cyborg rules/workflows do NOT take effect mid-run. Next Check-in
will see them. This prevents self-modifying execution.

# [07] Step 3: Team repo pulls (`{CyborgID}` branch)

## 7.1 Scan + branch-prep list
For each repo in §4.2:

```
if not exists(/Aliens/{Repo}/.git/):
    skipped.append((Repo, "not on disk"))
    continue

current = git branch --show-current
if current == "{CyborgID}":
    pull_batch.append(Repo)
    continue

# Need branch switch
git fetch origin {CyborgID}   # safe, no mutation to local state
if local_branch_exists("{CyborgID}"):
    # dirty-guard: stash first if working tree dirty
    if dirty: stash (Git_Pull contract)
    git checkout "{CyborgID}"
    pull_batch.append(Repo)
elif remote_branch_exists("origin/{CyborgID}"):
    if dirty: stash
    git checkout -b "{CyborgID}" origin/{CyborgID}
    pull_batch.append(Repo)
else:
    skipped.append((Repo, "CHECKIN_WARN_BRANCH_MISSING"))
```

## 7.2 Pull the batch
- If `pull_batch` non-empty, invoke ONCE:
  ```
  Workflow("Git_Pull", "{pull_batch_csv}", "Check-in: daily sync on branch {CyborgID}")
  ```
- Git_Pull already handles per-repo sequential pull + stash + merge safety.
- Collect per-repo status for the report.

## 7.3 Stash pops
- For repos where we stashed during branch-switch, `git stash pop` after
  pull completes on that repo (Git_Pull already pops for its own stashes;
  Check-in pops stashes it created here). Pop conflicts => surface as warn,
  never auto-resolve.

# [08] Step 4: Attendance repo + record event

## 8.1 Verify Attendance repo
- `/Aliens/Attendance/` exists AND has `.git/` (was just pulled in Step 3
  if present). Missing => HARD FAIL `CHECKIN_ERR_ATTENDANCE_REPO_MISSING`.

## 8.2 Ensure Attendance repo on `{CyborgID}` branch
- Same branch-prep rules as team repos (§7.1).
- If branch absent on remote too => HARD FAIL (attendance without correct
  branch means writes would go to wrong branch).

## 8.3 Record the event
```
Workflow("Attendance", "{CyborgID}", "checkin | {tz_label} | Check-in orchestrator")
```
Failure => SOFT FAIL, surface as `CHECKIN_WARN_ATTENDANCE_FAILED` in report
(session can continue; integrity of historical data is unharmed).

## 8.4 Refresh per-cyborg dashboard (cheap path)
```
Workflow("Attendance_Dashboard", "{CyborgID}", "Post-Check-in refresh")
```
- Single-cyborg scope only (no `ALL` full-scan here).
- Soft failure => `CHECKIN_WARN_DASHBOARD_FAILED`. Session continues.
- Repo-level Dashboard.md NOT rebuilt in Check-in path (scheduled `ALL` run or
  manual `Workflow("Attendance_Dashboard", "ALL", ...)` handles that).

# [09] Step 5: Session-start UX + Integrity Suite (10 tasks)

All 10 run in this order. Each is SOFT (a failure/warn in one does not block
the others; everything surfaces in the aggregate report).

## T1. Welcome banner
- Load `/Aliens/Attendance/Cyborg/{L1}/{L2}/{CyborgID}/Profile.json` if present
  (use sharding rule from `Attendance` §4).
- Print:
  ```
  ╔══════════════════════════════════════════════════════╗
  ║  Welcome back, {display or CyborgID}                 ║
  ║  Last seen:      {profile.last_seen}                 ║
  ║  Last event:     {profile.last_event}                ║
  ║  Streak:         {streaks.current_days} days         ║
  ║  Totals:         checkins={n}  checkouts={m}         ║
  ║  Today ({tz}):   {date_key}                          ║
  ╚══════════════════════════════════════════════════════╝
  ```
- First-time cyborg => "Welcome, new cyborg" variant.

## T2. Role reminder
- Read `/Aliens/Cyborg/AnilCyborg/Roles/Role.SoftwareEngineer/` index (if
  applicable to this cyborg — derived from manifest `Developer.Role`).
- Print a 1–2 line reminder: role name, today's focus hint.
- Missing role folder => `CHECKIN_WARN_ROLE_MISSING` (continue).

## T3. Pending planner CSV scan
- Scan `/Aliens/Project/{developer_username}/*.csv` (top-level only).
- For each CSV: count rows where `status` column == `0`.
- Print `{filename}: {count} pending` list, or `No pending planner CSVs.`
- **Do NOT** auto-start any row (strict "New conversation = NO auto-resume").

## T4. Recent activity digest (last 7 days)
- Derive from `Profile.json.recent_events` (cheap) and/or scan
  `Cyborg/{L1}/{L2}/{CyborgID}/{YYYY}/{YYYY-MM-DD}.jsonl` for the last 7 dates.
- Print per-day count + total hours (if checkouts present).

## T5. Branch sanity check
- For all repos in §4.1 + §4.2 that are present on disk:
  - Expected = `main` (critical) or `{CyborgID}` (team)
  - Actual   = `git branch --show-current`
  - Mismatch => `CHECKIN_WARN_BRANCH_DRIFT` with repo name + actual branch.
- Lists drift clearly so cyborg can fix before committing.

## T6. Manifest validation
- `/Aliens/manifest.json` must be valid JSON.
- Required keys: `Developer.Username`, `Approvals`, `Paths.WorkspaceRoot`.
- Missing/invalid => `CHECKIN_WARN_MANIFEST_INVALID` with specifics.

## T7. Secret health check
- Read-only verify: `/Aliens/Secret/` folder exists.
- Spot-check a small, agreed set of pointer files (names only; never print values):
  - `azure_openai.json` or `.env.azure_openai`
  - `elevenlabs.json`   or `.env.elevenlabs`
  - `pexels.json`       or `.env.pexels`
  - `unsplash.json`     or `.env.unsplash`
- Each present => `OK`; missing => `CHECKIN_WARN_SECRET_MISSING` with name.
- **Never log or print secret values.**

## T8. Clock drift check
- Compare `datetime.now(timezone.utc)` against an NTP source (best-effort,
  `time.windows.com` / `pool.ntp.org`). No internet => skip with `SKIPPED`.
- Drift > 30s => `CHECKIN_WARN_CLOCK_DRIFT` (attendance ts integrity at risk).

## T9. Disk space check
- Free space on `/Aliens` drive.
- `< 5 GB`  => `CHECKIN_WARN_DISK_LOW`
- `< 1 GB`  => `CHECKIN_WARN_DISK_CRITICAL` (more prominent surfacing)

## T10. Log rotation
- Target: `/Aliens/.Alien/{developer_username}/Runs/`
- Files older than **30 days** (mtime) → move to
  `/Aliens/.Alien/{developer_username}/Runs/_archive/{YYYY}/{MM}/`
- **No deletes.** Archive only.
- Missing Runs folder => skip silently.
- Failure => `CHECKIN_WARN_LOGROTATE_FAILED` (non-blocking).

# [10] Aggregate Status Report

```
=== Check-in Report ===
Cyborg:    {CyborgID}
Developer: {developer_username}
GitHub:    {github_account}
TZ:        {tz_label}
Started:   {ts_start_iso}
Finished:  {ts_end_iso}
Duration:  {seconds}s

--- Critical repos (main) ---
Cyborg:    {status}
.github:   {status}

--- Team repos (branch={CyborgID}) ---
| Repo        | On disk | Branch OK | Pull status | Commits | Notes |
|-------------|---------|-----------|-------------|---------|-------|
| Course_ACLE |   yes   |    yes    |  SUCCESS    |    3    |       |
| Feedback    |    no   |     -     |   SKIPPED   |    -    | not on disk |
| Project     |   yes   |    no     |   SKIPPED   |    -    | CHECKIN_WARN_BRANCH_MISSING |
...

--- Attendance ---
Event:     checkin
Record:    Cyborg/C1/C107/C1071/2026/2026-04-19.jsonl
Dashboard: Cyborg/C1/C107/C1071/Dashboard.md  (regenerated)
Status:    OK

--- Session suite (10 tasks) ---
T1  Welcome banner         : OK
T2  Role reminder          : OK
T3  Pending CSVs           : 2 file(s), 14 pending rows total
T4  Recent activity (7d)   : 5 days active, 27.3 hours
T5  Branch sanity          : OK
T6  Manifest validation    : OK
T7  Secret health          : 1 missing (elevenlabs)
T8  Clock drift            : +0.8s (OK)
T9  Disk space             : 42.1 GB free (OK)
T10 Log rotation           : 17 files archived

Overall: OK | PARTIAL | FAIL
```

Overall resolution:
- `FAIL`    = any step 1–2 (critical) failed, OR step 4 attendance HARD failure
- `PARTIAL` = any team repo issue, warn-level in suite, or soft-fail anywhere
- `OK`      = all green

# [11] Error / Warning Codes

| Code | Meaning | Severity |
|---|---|---|
| `ACC_ERR_TARGETS_REQUIRED`            | Cyborg ID missing                         | HARD |
| `ACC_ERR_DESCRIPTION_REQUIRED`        | TZ label missing                          | HARD |
| `CHECKIN_ERR_CRITICAL_REPO`           | Cyborg or .github missing/pull-failed     | HARD |
| `CHECKIN_ERR_ATTENDANCE_REPO_MISSING` | /Aliens/Attendance absent / wrong branch  | HARD |
| `CHECKIN_WARN_BRANCH_MISSING`         | `{CyborgID}` branch missing local+remote  | WARN |
| `CHECKIN_WARN_BRANCH_DRIFT`           | Repo on unexpected branch                 | WARN |
| `CHECKIN_WARN_PULL_PARTIAL`           | One or more team repos had issues         | WARN |
| `CHECKIN_WARN_ATTENDANCE_FAILED`      | Attendance soft-failed             | WARN |
| `CHECKIN_WARN_ROLE_MISSING`           | Cyborg role folder absent                 | WARN |
| `CHECKIN_WARN_MANIFEST_INVALID`       | manifest.json schema/required-field issue | WARN |
| `CHECKIN_WARN_SECRET_MISSING`         | Known secret pointer absent               | WARN |
| `CHECKIN_WARN_CLOCK_DRIFT`            | Drift > 30s vs NTP                        | WARN |
| `CHECKIN_WARN_DISK_LOW`               | Free space < 5 GB                         | WARN |
| `CHECKIN_WARN_DISK_CRITICAL`          | Free space < 1 GB                         | WARN |
| `CHECKIN_WARN_LOGROTATE_FAILED`       | Log archive move failed                   | WARN |

# [12] Permanently Forbidden
- Cloning any repo (critical or team)
- Creating files outside child-workflow scope / UX outputs / log archive
- Running any workflow NOT listed in `ChildWorkflows`
- Auto-creating the `{CyborgID}` branch on any repo
- Auto-starting any planner CSV row
- Parallel `Git_Pull`
- `git reset --hard`, `git clean -fd`, force-push, rebase (transitive from Git_Pull)
- Logging/printing any secret value

# [13] Re-Read Rule (per global SSOT)
Before every Check-in execution, agent MUST re-read:
- `Workflows/ACC/Session/Check-in.md` (this file)
- `Workflows/ACC/Git/Git_Pull.md`
- `Workflows/ACC/Attendance/Attendance.md`

# [14] Done Criteria
- Both critical repos on `main`, pulled successfully
- All present team repos on `{CyborgID}`; each has individual status
- Attendance `checkin` event recorded (or explicit warn reported)
- All 10 session-suite tasks executed (ok/warn/skip recorded)
- Aggregate report generated with Overall = OK / PARTIAL / FAIL
