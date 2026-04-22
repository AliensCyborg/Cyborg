---
RefId: ACC.ACLE
Type: ACC.Reference
Scope: ACLE_Architecture
Status: Active
Version: 26.09.00
LastUpdated: 2026-04-14
Audience: "AlienCyborg Agent + ACC Workflow Authors"
Purpose: >
  ACLE (Aliens Cinematic Learning Experience) ecosystem ki architecture, assets, HTML template rules,
  validation checklist, aur post-conversion requirements define karna.
  Yeh reference ALL ACLE workflows (Blog_ACLE, Blogs_ACLE, Course_ACLE, Skill_ACLE, Wiki_ACLE,
  Wikipedia_ACLE, Role_ACLE) ke liye SSOT hai.
NonNegotiables:
  - CentralAssetsOnly: true
  - NoLocalAssets: true
  - MandatoryDOMElements: true
  - ScriptEscapeRule: true
  - PostConversionValidation: true
  - CourseIndexJSON: true
  - ThemeToggleSupport: true
  - LessonNavigation: true
  - CleanURLs: true
  - NoHtmlExtension: true
  - NoIndexInHref: true
  - SEOMetaTags: true
  - RootHtaccess: true
---

# ACC.ACLE.md — ACLE Ecosystem Architecture Reference

## [01] Overview (Hinglish)
ACLE (Aliens Cinematic Learning Experience) ek ecosystem hai jisme source content (Course, Skill, Wiki,
Blog, Blogs, Wikipedia) ko cinematic HTML video lessons me convert kiya jata hai.

Is reference me:
- Central asset architecture (SSOT)
- Per-project asset path depth rules
- HTML template mandatory DOM elements
- Script content escape rules
- Post-conversion validation checklist
- Index.html navigation system requirements
- Known bugs aur their fixes

> IMPORTANT: Yeh reference ALL ACLE workflows ko apply hoti hai.
> Koi bhi ACLE workflow run karne se pehle yeh ref read karna MANDATORY hai.

---

## [02] ACLE Ecosystem Map

### [02.1] Central Asset Repository (SSOT)
```
/Aliens/ACLE/assets/
    css/aliens-cinematic.css       (cinematic stylesheet — production)
    js/aliens-cinematic.js         (cinematic engine — production)
    js/aliens-cinematic-raw.js     (cinematic engine — unminified/debug)
    svg/aliens-logo-defs.svg       (shared SVG logo definitions)
```

- ALL ACLE projects use assets from this ONE central location.
- Local `assets/` folder inside ANY *_ACLE repo is **FORBIDDEN** — must NOT exist.
- Assets update sirf `/Aliens/ACLE/assets/` me hoga, individual repos me kabhi nahi.
- Agar kisi *_ACLE repo me `assets/` folder mile, it's a legacy artifact — DELETE it.

### [02.2] ACLE Consumer Projects
| # | Project | Source Repo | Output Repo | Source Format | Branding |
|---|---------|------------|-------------|---------------|----------|
| 1 | Blog_ACLE | `/Aliens/Blog/` | `/Aliens/Blog_ACLE/` | `.md` | Aliens Blog |
| 2 | Blogs_ACLE | `/Aliens/Blogs/` | `/Aliens/Blogs_ACLE/` | `.html` | Aliens Blogs |
| 3 | Course_ACLE | `/Aliens/Course/` | `/Aliens/Course_ACLE/` | `.md` | Aliens Course |
| 4 | Skill_ACLE | `/Aliens/Skills/` | `/Aliens/Skill_ACLE/` | `.md` | Aliens School |
| 5 | Wiki_ACLE | `/Aliens/Wiki/` | `/Aliens/Wiki_ACLE/` | `.md` | Aliens Wiki |
| 6 | Wikipedia_ACLE | `/Aliens/Wikipedia/` | `/Aliens/Wikipedia_ACLE/` | `.md` | Aliens Wikipedia |
| 7 | Role_ACLE | `/Aliens/Role/` | `/Aliens/Role_ACLE/` | `.md` | Aliens Role |

### [02.3] Master Hub
- `/Aliens/ACLE/index.html` — master hub linking to all 6 ACLE projects
- Dark cinematic UI with project cards, stats, gradient glow effects

---

## [03] Project Structures (Confirmed)

### [03.1] Course_ACLE
```
Course_ACLE/
    HIEN/
        {Program}/                          # e.g. Advanced_Data_Analytics
            {Course}/                       # e.g. C1_Foundations_of_Data_Science
                {Module}/                   # e.g. M1_Introduction_to_Data_Science
                    {Lesson}.html           # e.g. L1_Welcome_to_the_Certificate.html
                    {Lesson}_narrations.json
                    audio_{Lesson}/
                        slide_01.mp3, slide_02.mp3, ...
```
- Depth from repo root: **5 levels** (HIEN/Program/Course/Module/Lesson.html)
- Stats (as of 2026-06): 44 programs, 346 courses, 1457 modules, 6439 lessons

### [03.2] Skill_ACLE
```
Skill_ACLE/
    HIEN/
        {Topic}/                            # e.g. Python, Accountability
            {Lesson}.html                   # e.g. 01_Python_Introduction.html
            {Lesson}_narrations.json
            audio_{Lesson}/
                slide_01.mp3, slide_02.mp3, ...
```
- Depth from repo root: **3 levels** (HIEN/Topic/Lesson.html)
- Stats (as of 2026-06): 61 topics, 2538 lessons

### [03.3] Wiki_ACLE
```
Wiki_ACLE/
    HIEN/
        {Letter}/                           # e.g. A, B, C, D
            {Article}.html                  # e.g. Algorithm.html
            {Article}_narrations.json
            audio_{Article}/
                slide_01.mp3, slide_02.mp3, ...
```
- Depth from repo root: **3 levels** (HIEN/Letter/Article.html)
- Stats (as of 2026-06): 4 letters (A-D), 2866 articles

### [03.4] Blog_ACLE
```
Blog_ACLE/
    {Language}/                             # e.g. HIEN, EN, HI
        {ArticleName}.html
        {ArticleName}_narrations.json
        audio_{ArticleName}/
```
- Depth from repo root: **2 levels** (Language/Article.html)
- No letter sub-folders — flat per language

### [03.5] Blogs_ACLE
```
Blogs_ACLE/
    {BlogName}/                             # e.g. Healthline
        {Language}/                         # e.g. EN
            {Slug}.html
            {Slug}_narrations.json
            audio_{Slug}/
```
- Depth from repo root: **3 levels** (BlogName/Language/Slug.html)

### [03.6] Wikipedia_ACLE
```
Wikipedia_ACLE/
    {Language}/                             # e.g. HIEN
        {LetterSeq}/                        # e.g. A0001, B0002
            {ArticleName}.html
            {ArticleName}_narrations.json
            audio_{ArticleName}/
```
- Depth from repo root: **3 levels** (Language/LetterSeq/Article.html)

---

## [04] Asset Path Depth Rules (NON-NEGOTIABLE)

HTML file se central ACLE assets tak relative path calculate karna:
- File ka parent directory se workspace root (`/Aliens/`) tak `../` count karo
- Phir `ACLE/assets/` append karo

### Per-Project Asset Path Table (SSOT)
| Project | HTML Location (from repo root) | Levels to /Aliens/ | Correct Relative Path |
|---------|-------------------------------|--------------------|-----------------------|
| Blog_ACLE | `{Lang}/{Article}.html` | 2 | `../../ACLE/assets/` |
| Blogs_ACLE | `{Blog}/{Lang}/{Slug}.html` | 3 | `../../../ACLE/assets/` |
| Course_ACLE | `HIEN/{Prog}/{Course}/{Mod}/{Lesson}.html` | 5 | `../../../../../ACLE/assets/` |
| Skill_ACLE | `HIEN/{Topic}/{Lesson}.html` | 3 | `../../../ACLE/assets/` |
| Wiki_ACLE | `HIEN/{Letter}/{Article}.html` | 3 | `../../../ACLE/assets/` |
| Wikipedia_ACLE | `{Lang}/{LetterSeq}/{Article}.html` | 3 | `../../../ACLE/assets/` |\n| Role_ACLE | `{RoleSlug}.html` | 1 | `../ACLE/assets/` |

### Asset References in HTML (Template)
```html
<!-- CSS -->
<link rel="stylesheet" href="{ASSET_PATH}css/aliens-cinematic.css">

<!-- JS (pick one) -->
<script src="{ASSET_PATH}js/aliens-cinematic.js"></script>
<!-- OR for debug: -->
<script src="{ASSET_PATH}js/aliens-cinematic-raw.js"></script>

<!-- SVG (inline use) -->
<!-- SVG defs are embedded inline or loaded from {ASSET_PATH}svg/aliens-logo-defs.svg -->
```

### WRONG Path Examples (FORBIDDEN)
- `../assets/` — local assets reference (WRONG)
- `../../assets/` — local assets reference (WRONG)
- `./assets/` — local assets reference (WRONG)
- `assets/` — local assets reference (WRONG)
- Any path that does NOT contain `ACLE/assets/` is WRONG.

---

## [05] HTML Template Mandatory DOM Elements (NON-NEGOTIABLE)

### [05.1] Problem Statement
`aliens-cinematic.js` internally calls `document.getElementById('pen-canvas').getContext('2d')` along
with several other DOM element lookups. Agar yeh elements HTML me missing hon, to JavaScript error
aata hai aur page 100% loading par stuck reh jata hai.

### [05.2] Required Element Block
Yeh elements EVERY generated ACLE HTML file me MANDATORY hain.
Placement: AFTER `<div id="audio-toast"></div>`, BEFORE the closing JS script references.

```html
<!-- ====================== Mode / Recording / Countdown ====================== -->
<div id="mode-panel" class="mode-panel" style="display:none;">
    <span id="mode-label">Presenter Mode</span>
</div>
<div id="rec-indicator" class="rec-indicator" style="display:none;">
    <span class="rec-dot"></span> REC
</div>
<div id="vid-countdown" class="vid-countdown" style="display:none;"></div>
<div id="vid-end-overlay" class="vid-end-overlay" style="display:none;">
    <div class="end-msg">Recording Complete</div>
</div>

<!-- ====================== Presenter Panel ====================== -->
<div id="presenter-panel" class="presenter-panel" style="display:none;">
    <div id="presenter-notes" class="presenter-notes"></div>
    <div class="presenter-controls">
        <button id="prev-slide-btn" class="ctrl-btn">&#9664; Prev</button>
        <span id="presenter-slide-counter" class="slide-counter">1 / 1</span>
        <button id="next-slide-btn" class="ctrl-btn">Next &#9654;</button>
    </div>
    <div class="presenter-timer" id="presenter-timer">00:00</div>
</div>

<!-- ====================== Pen / Laser Tools ====================== -->
<canvas id="pen-canvas" class="pen-canvas"></canvas>
<div id="pen-toolbar" class="pen-toolbar" style="display:none;">
    <button class="pen-btn" data-tool="pen" title="Pen">&#9998;</button>
    <button class="pen-btn" data-tool="highlighter" title="Highlighter">&#9730;</button>
    <button class="pen-btn active" data-tool="laser" title="Laser Pointer">&#128308;</button>
    <button class="pen-btn" data-tool="eraser" title="Eraser">&#9746;</button>
    <button class="pen-btn" data-tool="clear" title="Clear All">&#128465;</button>
    <input type="color" id="pen-color" class="pen-color" value="#ff0000" title="Pick Color">
    <input type="range" id="pen-size" class="pen-size" min="1" max="20" value="3" title="Brush Size">
</div>
<div id="pen-laser" class="pen-laser" style="display:none;"></div>
```

### [05.3] Element IDs Checklist
Yeh IDs generated HTML me MUST exist (missing = page stuck at loading):
- `mode-panel`, `mode-label`
- `rec-indicator`
- `vid-countdown`
- `vid-end-overlay`
- `presenter-panel`, `presenter-notes`, `prev-slide-btn`, `presenter-slide-counter`, `next-slide-btn`, `presenter-timer`
- `pen-canvas`
- `pen-toolbar`, `pen-color`, `pen-size`
- `pen-laser`

Total: **16 mandatory element IDs**

### [05.4] Root Cause Reference
- `aliens-cinematic.js` line ~821: `document.getElementById('pen-canvas').getContext('2d')`
- Without `pen-canvas`, this throws `Cannot read property 'getContext' of null`
- Browser stops executing further JS → cinematic engine never initializes → stuck at loading screen

---

## [06] Script Content Escape Rule (NON-NEGOTIABLE)

### [06.1] Problem
HTML me `<script>` block ke andar agar LESSON_ JavaScript string variables me literal `</script>` ho,
browser prematurely script block close kar deta hai. Result: broken JavaScript, page crash/blank.

### [06.2] Fix
Har `</script>` inside LESSON_ variable string values ko `<\/script>` se escape karo.

### [06.3] Examples
```javascript
// WRONG — browser breaks here:
var LESSON_CONTENT = "...<script>alert('test')</script>...";

// RIGHT — escaped properly:
var LESSON_CONTENT = "...<script>alert('test')<\/script>...";
```

### [06.4] Scope
- Yeh rule sirf LESSON_ prefixed variable values ke andar apply hota hai
  (LESSON_CONTENT, LESSON_NARRATIONS, LESSON_META, etc.)
- Actual closing `</script>` tags (real HTML structure) ko escape NAHI karna
- Converter scripts ko yeh escape internally handle karna chahiye
- Post-conversion check: grep for literal `</script>` inside LESSON_ lines — count should be 0

### [06.5] Affected Scale (Historical Reference)
- Course_ACLE: 0 files affected (converter already handled)
- Skill_ACLE: 101 files fixed in bulk (2026-06)
- Wiki_ACLE: 17 files fixed in bulk (2026-06)

---

## [07] HTML Integrity Rules

### [07.1] Single Closing Tags
- `</body></html>` MUST appear exactly ONCE at the very end of the file
- Duplicate `</body></html>` or partial HTML fragments after the closing = CORRUPT file
- Post-conversion check: count occurrences of `</body>` — must be exactly 1

### [07.2] No Truncation
- HTML file me abrupt truncation (incomplete tags, missing closers) = CORRUPT
- Minimum file size: 5KB (anything smaller is likely corrupt/empty)

### [07.3] Historical Reference
- 3 files found with duplicate `</body></html>` + partial fragments (2026-06)
- Root cause: converter script had buffering issue, fixed by truncation cleanup

---

## [08] Post-Conversion Validation Checklist (MANDATORY)

After EVERY batch conversion or individual conversion, yeh checks run karo:

### [08.1] Asset Path Check
```powershell
# Count files with local asset references (should be ZERO)
$old = 0
$files = [System.IO.Directory]::GetFiles('{RepoPath}/HIEN', '*.html', [System.IO.SearchOption]::AllDirectories)
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f)
    if($c.Contains('../assets/') -or $c.Contains('./assets/') -or $c.Contains('assets/css/') -and -not $c.Contains('ACLE/assets/')) {
        $old++
    }
}
# $old MUST be 0
```

### [08.2] DOM Elements Check
```powershell
# Sample 5 random HTML files, verify pen-canvas exists
$files = [System.IO.Directory]::GetFiles('{RepoPath}/HIEN', '*.html', [System.IO.SearchOption]::AllDirectories)
$sample = $files | Get-Random -Count 5
foreach($f in $sample) {
    $c = [System.IO.File]::ReadAllText($f)
    if(-not $c.Contains('id="pen-canvas"')) {
        Write-Host "MISSING pen-canvas: $f"
    }
    if(-not $c.Contains('id="mode-panel"')) {
        Write-Host "MISSING mode-panel: $f"
    }
    if(-not $c.Contains('id="presenter-panel"')) {
        Write-Host "MISSING presenter-panel: $f"
    }
}
```

### [08.3] Script Escape Check
```powershell
# Find LESSON_ lines containing unescaped </script>
$bad = 0
foreach($f in $files) {
    $lines = [System.IO.File]::ReadAllLines($f)
    foreach($line in $lines) {
        if($line -match 'LESSON_' -and $line.Contains('</script>')) {
            $bad++
        }
    }
}
# $bad MUST be 0
```

### [08.4] HTML Integrity Check
```powershell
# Verify single </body></html> at end
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f)
    $bodyCount = ([regex]::Matches($c, '</body>')).Count
    if($bodyCount -ne 1) {
        Write-Host "CORRUPT (body count=$bodyCount): $f"
    }
}
```

### [08.5] Local Assets Folder Check
```powershell
# This folder must NOT exist
Test-Path '{RepoPath}/assets'
# MUST return False
```

### [08.6] Course Index JSON Check
```powershell
# Verify course-index.json exists in each topic folder
$missing = 0
foreach($dir in (Get-ChildItem '{RepoPath}/HIEN' -Directory)) {
    if(-not (Test-Path "$($dir.FullName)/course-index.json")) {
        $missing++
        Write-Host "MISSING course-index.json: $($dir.Name)"
    }
}
# $missing MUST be 0
```

### [08.7] Summary Gates
| Check | Expected | Action if Failed |
|-------|----------|-----------------|
| Local asset refs | 0 | Bulk fix paths to central ACLE |
| pen-canvas missing | 0 | Bulk insert DOM elements block |
| Unescaped </script> | 0 | Bulk escape in LESSON_ lines |
| Duplicate </body> | 0 | Truncate corrupt endings |
| Local assets/ folder | Not exists | Delete folder |
| course-index.json | Exists per topic | Generate missing JSONs |

---

## [09] Index.html Navigation System (Post-Conversion)

### [09.1] Purpose
Har ACLE project ko browseable navigation chahiye — users topics/programs/lessons discover kar sakein.

### [09.2] Project Root Index
- File: `/Aliens/{Repo}/index.html`
- Content: Dark cinematic card grid listing all topics/programs/letters
- Features:
  - Search/filter bar
  - Responsive grid layout
  - Breadcrumbs (ACLE > Project)
  - Per-card stats (lesson count, article count)
  - Gradient glow effects on hover

### [09.3] Topic-Level Indexes
- File: `/Aliens/{Repo}/HIEN/{Topic_or_Program_or_Letter}/index.html`
- Content: All lessons under that topic with direct links
- Features:
  - Search bar
  - Breadcrumbs (ACLE > Project > Topic)
  - Collapsible hierarchy (for Course: Course > Module > Lesson)
  - Direct lesson links

### [09.4] Index Style Rules
- Background: `#0a0a0a` (dark)
- Primary colors: `#22c55e` (green), `#0ea5e9` (blue), `#7c6aff` (purple)
- No external CSS dependency — CSS MUST be inline in index files
- Responsive: mobile + desktop
- Minimal JavaScript — pure CSS where possible, inline JS only if needed

### [09.5] Generation Trigger
- Index files banaye jayen AFTER batch conversion completes (not during)
- Re-generation safe: existing index.html overwrite allowed
- Index generator ko lesson count dynamically read karna chahiye (hardcode forbidden)

### [09.6] Stats (Historical Reference, 2026-04)
- Total index files generated: 113
  - 1 ACLE master hub
  - 3 project-level indexes (Course_ACLE, Skill_ACLE, Wiki_ACLE)
  - 44 Course program indexes
  - 61 Skill topic indexes
  - 4 Wiki letter indexes

### [09.7] Index Pages Theme Support
- All index.html files include inline `body.light-theme` CSS overrides
- Theme toggle button: fixed top-right, z-index 9000
- Uses same `aliens-theme` localStorage key as lesson pages
- Keyboard shortcut: `T` key

### [09.8] JSON Course Index Files (NEW)
- Separate from index.html files, these JSON files power the in-lesson Course Index Panel
- Total: 105 files (44 Course_ACLE + 61 Skill_ACLE)
- Must be regenerated after new lessons added
- See Section [11] for full JSON schema and panel details

---

## [10] Dark/Light Theme Toggle System

### [10.1] Overview
ACLE cinematic player supports dark (default) and light theme via `body.light-theme` CSS class.
Theme persists across pages via `localStorage` key `aliens-theme`.

### [10.2] CSS Implementation
- `body.light-theme` section in `aliens-cinematic.css` overrides ~100 CSS variables/rules
- Covers: core vars, slide backgrounds (bg-1..bg-10), typography, cards, components, quiz, timeline,
  controls, top-bar, subtitle-bar, toast, kb-hint, progress bar, loader, spotlight,
  mode panel, presenter panel, pen toolbar, spectrum, countdown/end overlays, course panel, lesson nav
- Theme button dynamically injected into `#mode-panel` as first child by JS

### [10.3] JS Implementation
- `toggleTheme()` — toggles `light-theme` class on body, saves to localStorage, updates button icon/toast
- `initTheme()` IIFE — loads preference from localStorage, falls back to OS `prefers-color-scheme`
- Keyboard shortcut: `T` key toggles theme

### [10.4] Index Pages
- All 113+ index.html files have inline `body.light-theme` CSS overrides
- `.theme-toggle` fixed button (top-right, z-index 9000)
- Same `aliens-theme` localStorage key for cross-page persistence

### [10.5] Integration Rules
- Theme button is auto-injected — no HTML template changes needed
- New CSS components MUST include `body.light-theme` overrides
- localStorage key `aliens-theme` is shared across lessons + index pages

---

## [11] Course Index Panel (Left Sidebar)

### [11.1] Overview
`#course-panel` is a 380px fixed left sidebar showing the full course/skill structure as a tree.
Data is loaded from `course-index.json` files via `fetch()`.

### [11.2] JSON Index Files (SSOT)
Each course/skill folder has a `course-index.json` file:

**Course type** (nested hierarchy):
```json
{
  "type": "course",
  "name": "AI",
  "modules": [
    {
      "code": "C1_Introduction_to_AI",
      "name": "Introduction to AI",
      "submodules": [
        {
          "code": "M1_What_Is_AI",
          "name": "What Is AI",
          "lessons": [
            { "file": "L1_Artificial_Intelligence_ko_Define_Karna.html", "name": "Artificial Intelligence ko Define Karna" }
          ]
        }
      ]
    }
  ]
}
```

**Skill type** (flat list):
```json
{
  "type": "skill",
  "name": "AI",
  "lessons": [
    { "file": "00-Index.html", "name": "Index" },
    { "file": "01-Machine-Learning-Intro.html", "name": "Machine Learning Intro" }
  ]
}
```

### [11.3] JSON File Locations
| Project | JSON Path | Count |
|---------|-----------|-------|
| Course_ACLE | `Course_ACLE/HIEN/{Program}/course-index.json` | 44 |
| Skill_ACLE | `Skill_ACLE/HIEN/{Topic}/course-index.json` | 61 |
Total: **105 JSON files** (as of 2026-04)

### [11.4] JSON Generation
- Generated via PowerShell scan of folder structure
- Course: scans C#_ > M#_ > L#_.html hierarchy
- Skill: scans numbered .html files in flat folder
- JSON regeneration safe (overwrite allowed)
- MUST regenerate after new lessons are added

### [11.5] Panel UI Features
- Course type: Expandable tree — Modules → Submodules → Lessons (collapse/expand)
- Skill type: Flat numbered list with skill numbers + names
- Current lesson highlighted (green accent, auto-expanded, auto-scrolled)
- Search/filter not included (hierarchy is self-organizing)
- Toggle: 📚 button in mode panel (dynamically injected)
- Keyboard: `I` key toggles panel

### [11.6] Layout Integration
- When open: `body.index-mode` class applied
- Player/controls/top-bar/progress shift right by 380px
- Combined with presenter mode: both sidebars visible (player shrinks in middle)
- Mobile: overlay mode (85vw max, z-index 9500, does NOT push content)

---

## [12] Lesson Navigation System (Prev/Next)

### [12.1] Overview
Bottom fixed navigation bar (`#lesson-nav`) with Previous and Next lesson links.
Data comes from `course-index.json` (same as Course Index Panel).

### [12.2] UI
- `#lesson-nav` fixed at bottom with two `lnav-btn` links
- Desktop: `bottom:120px` (above subtitle-bar at 74px)
- Mobile: `bottom:178px` (above subtitle-bar at 132px)
- Shows lesson names with ← / → arrows
- Truncates long names with text-overflow
- Appears 2 seconds after page load (smooth fade-in)
- Mobile: smaller buttons (42vw max-width each)
- z-index: 110

### [12.3] Keyboard Shortcuts
- `Shift+N` — navigate to next lesson
- `Shift+B` — navigate to previous lesson (B = Back)

### [12.4] Navigation Logic
- JS builds ordered lesson list from course-index.json
- Finds current lesson by filename match
- Sets `_prevLesson` and `_nextLesson` accordingly
- Course type: navigates across modules/submodules seamlessly
- Skill type: simple sequential (01 → 02 → 03...)

### [12.5] Lesson Complete Overlay
`#lesson-end-overlay` shows when last slide's audio ends:
- "✅ Lesson Complete!" title
- Action buttons:
  - **Next lesson** (primary green) — if next lesson exists
  - **Previous lesson** (secondary)
  - **Replay** — hides overlay, user can restart
  - **Course Index** — opens left sidebar
- Backdrop blur overlay with smooth fade-in animation
- Light theme supported

---

## [13] Updated Keyboard Shortcuts (Complete)

| Key | Action |
|-----|--------|
| `→` | Next slide |
| `←` | Previous slide |
| `Space` | Toggle play/pause |
| `P` | Toggle play/pause |
| `M` | Toggle narration on/off |
| `F` | Toggle fullscreen |
| `T` | Toggle dark/light theme |
| `I` | Toggle course index panel |
| `L` | Toggle presenter/Live Class mode |
| `D` | Toggle drawing mode (in presenter) |
| `V` | Toggle video recording mode |
| `?` | Show keyboard shortcuts help |
| `Shift+N` | Go to next lesson |
| `Shift+B` | Go to previous lesson |
| `Ctrl+Z` | Undo pen stroke (in drawing mode) |

---

## [14] Updated Mandatory DOM Element IDs

### [14.1] Full ID Checklist (21 elements)
Previous 16 IDs remain required. Additional 5 new IDs are dynamically created by JS:
- `btn-theme` — theme toggle button (injected into mode-panel by initTheme)
- `btn-index` — course index button (injected into mode-panel by _injectIndexButton)
- `course-panel` — left sidebar (created by _buildCoursePanel)
- `lesson-nav` — prev/next navigation bar (created by _buildLessonNav)
- `lesson-end-overlay` — lesson complete overlay (created by _buildEndOverlay)

These 5 elements are **dynamically injected by JS** — no HTML template changes required.
The original 16 mandatory static elements still MUST be in HTML template.

### [14.2] Static Elements (in HTML template = 16):
`mode-panel`, `mode-label`, `rec-indicator`, `vid-countdown`, `vid-end-overlay`,
`presenter-panel`, `presenter-notes`, `prev-slide-btn`, `presenter-slide-counter`,
`next-slide-btn`, `presenter-timer`, `pen-canvas`, `pen-toolbar`, `pen-color`,
`pen-size`, `pen-laser`

### [14.3] Dynamic Elements (injected by JS = 5):
`btn-theme`, `btn-index`, `course-panel`, `lesson-nav`, `lesson-end-overlay`

---

## [15] Presenter Panel (Updated)

### [15.1] Width
- Desktop: **420px** (previously 330px)
- Mobile: 100vw × 50vh at bottom
- Fixed right side, z-index 8600

### [15.2] Layout Adjustments (when active)
Desktop:
- `#player` width: `calc(100vw - 420px)`
- `#top-bar` right: 420px
- `#controls` left: `calc((100vw - 420px) / 2)`
- `#progress-wrap` right: 420px
- `#subtitle-bar` centered in available area
- `#mode-panel` right: 436px

Mobile:
- Presenter panel goes to bottom 50vh
- Player remains full width, shifted up by margin-bottom: 50vh
- Mode panel stays centered at bottom

---

## [16] Mobile Layout Rules (Updated)

### [16.1] Breakpoint
`@media(max-width:768px)` for all mobile overrides

### [16.2] Mode Panel (Mobile)
- Position: `bottom:20px; left:50%; transform:translateX(-50%)`
- Direction: `flex-direction:row` (horizontal)
- Buttons: 36px × 36px
- Tooltips hidden

### [16.3] Controls (Mobile)
- Controls bar: `bottom:76px` (above mode panel)
- Progress bar: `bottom:120px` (above controls)
- Subtitle bar: `bottom:132px; max-width:80vw`

### [16.4] Course Panel (Mobile)
- Width: 85vw (max 340px)
- z-index: 9500 (overlay, does NOT push content)
- Player/controls do NOT shift when course panel open on mobile

### [16.5] Lesson Nav (Desktop + Mobile)
- Desktop bottom: **120px** (above subtitle at 74px)
- Mobile bottom: **178px** (above subtitle at 132px)
- Mobile button max width: 42vw
- Mobile font size: 0.65rem
- Presenter-mode: centered in available area (`left:calc((100vw - 420px) / 2)`)
- Index-mode: shifted right of panel (`left:calc(380px + (100vw - 380px) / 2)`)

---

## [17] Known Bugs — Historical Reference

### [17.1] Bug: Page Stuck at 100% Loading
- Root cause: Missing DOM elements (pen-canvas, mode-panel, presenter-panel, etc.)
- Impact: 5,404 files (2,538 Skill_ACLE + 2,866 Wiki_ACLE)
- Fix: Bulk insertion of complete DOM elements block after audio-toast div
- Ref: Section [05]

### [17.2] Bug: Broken JavaScript / Page Crash
- Root cause: Unescaped `</script>` inside LESSON_ string variables
- Impact: 118 files (101 Skill_ACLE + 17 Wiki_ACLE)
- Fix: Replace `</script>` with `<\/script>` in LESSON_ lines only
- Ref: Section [06]

### [17.3] Bug: Duplicate HTML Closers
- Root cause: Converter buffering issue producing duplicate `</body></html>` + fragments
- Impact: 3 files
- Fix: Truncate file at first valid `</body></html>`
- Ref: Section [07]

### [17.4] Bug: Wrong Asset Path (Local Instead of Central)
- Root cause: Converter used `../../assets/` or `../../../assets/` pointing to local assets
- Impact: 5,326+ files across Skill_ACLE and Wiki_ACLE
- Fix: Bulk replace local asset paths with central ACLE asset paths
- Ref: Section [04]

### [17.5] Issue: Stale Local Assets
- Root cause: `Course_ACLE/assets/` and `Skill_ACLE/assets/` had outdated copies of central assets
- Impact: Users could get old CSS/JS if local path was used
- Fix: Delete local assets folders entirely (they should never exist)
- Ref: Section [02.1]

---

## [18] Converter Scripts — Quick Reference

| Project | Single Converter | Batch Orchestrator |
|---------|-----------------|-------------------|
| Blog_ACLE | `convert_blog_acle.py` | `batch_blog_acle.py` |
| Blogs_ACLE | `convert_blogs_acle.py` | `batch_blogs_acle.py` |
| Course_ACLE | `convert_lesson.py` | `batch_all_programs.py` |
| Skill_ACLE | `convert_skill.py` | `batch_skill.py` |
| Wiki_ACLE | `convert_article.py` | `batch_wiki.py` |
| Wikipedia_ACLE | `convert_wikipedia.py` | `batch_wikipedia_acle.py` |

Scripts location: `/Aliens/Cyborg/AlienCyborg/Code/`
Scripts are READ-ONLY during workflow execution.

---

## [19] PowerShell Terminal Rules (ACLE specific)

### [19.1] Remove-Item Blocked
- `Remove-Item` may be blocked by security policy on some machines
- Alternative: `[System.IO.Directory]::Delete($path, $true)` for folders
- Alternative: `[System.IO.File]::Delete($path)` for files

### [19.2] File Operations
- Read: `[System.IO.File]::ReadAllText($path)` or `[System.IO.File]::ReadAllText($path, $enc)`
- Write: `[System.IO.File]::WriteAllText($path, $content, $enc)`
- UTF-8 without BOM: `$enc = New-Object System.Text.UTF8Encoding($false)`

### [19.3] Bulk Operations
- For bulk file operations (1000+ files), use PowerShell scripts with `[System.IO]` APIs
- Avoid `Get-Content`/`Set-Content` for large batches (slow + encoding issues)
- Use `[System.IO.Directory]::GetFiles($path, '*.html', [System.IO.SearchOption]::AllDirectories)`

---

## [20] Clean URL System (NON-NEGOTIABLE)

### [20.1] Overview
Aliens School domain (`aliens.school`) par `.html` extension URL me dikhna **FORBIDDEN** hai.
Root `.htaccess` (located at `/Aliens/.htaccess`) handles:
- `.html` → extensionless URL (301 redirect)
- `/path/index` → `/path/` (301 redirect)
- Extensionless URL → internally serves `.html` file

### [20.2] href Rules (ALL ACLE HTML Files)
| Pattern | Correct | WRONG |
|---------|---------|-------|
| Link to project root | `href="../../../Course_ACLE/"` | `href="../../../Course_ACLE/index"` or `href="../../../Course_ACLE/index.html"` |
| Link to hub | `href="../../../ACLE/"` | `href="../../../ACLE/index"` or `href="../../../ACLE/index.html"` |
| Link to lesson | `href="L1_Welcome"` | `href="L1_Welcome.html"` |
| Link to current dir index | `href="./"` | `href="index"` or `href="index.html"` |
| Cross-module lesson | `href="../../C1_Foundations/M1_Intro/L1_Welcome"` | `href="../../C1_Foundations/M1_Intro/L1_Welcome.html"` |

### [20.3] Rules Summary
- Kisi bhi internal `href` me `.html` extension FORBIDDEN hai
- Kisi bhi `href` me `index` as filename FORBIDDEN hai — directory URL (`./` ya `../path/`) use karo
- External links (googleapis, CDN, etc.) ko touch mat karo
- `data-md` attribute me `.md` extension RAKHNI hai (yeh file reference hai, URL nahi)
- `data-index` attribute me `.json` extension RAKHNI hai
- CSS/JS asset references me `.css`/`.js` extension RAKHNI hai

### [20.4] Root .htaccess (SSOT Location)
- File: `/Aliens/.htaccess`
- This file handles ALL URL rewriting for aliens.school
- Per-project .htaccess files are NOT needed (root handles everything)
- .htaccess features:
  - HTTPS force
  - /path/index → /path/ redirect
  - .html extension strip (301)
  - Extensionless → .html internal serve
  - Security headers
  - Static asset caching
  - Gzip compression
  - .md files are NOT blocked (ATLE/ACLE engine fetches them via AJAX)

---

## [21] SEO Meta Tags (NON-NEGOTIABLE)

### [21.1] Overview
Har generated ACLE HTML file me SEO meta tags MANDATORY hain — Google Search Console submission ke liye.

### [21.2] Required Tags
```html
<title>{Lesson Title} — Aliens School</title>
<meta name="description" content="{Clean title text}">
<meta name="robots" content="index, follow">
<link rel="canonical" href="https://aliens.school/{clean-path-without-html}">
<meta property="og:type" content="article">
<meta property="og:title" content="{Clean title text}">
<meta property="og:description" content="{Clean title text}">
<meta property="og:url" content="https://aliens.school/{clean-path-without-html}">
<meta property="og:site_name" content="Aliens School">
<meta name="twitter:card" content="summary">
<meta name="twitter:title" content="{Clean title text}">
<meta name="twitter:description" content="{Clean title text}">
```

### [21.3] Canonical URL Rules
- Domain: `https://aliens.school`
- Path: file path relative to `/Aliens/` root, with `.html` stripped
- Index pages: `/path/to/` (not `/path/to/index`)
- Example lesson: `https://aliens.school/Course_ACLE/HIEN/AI/C1_Introduction/M1_What_Is_AI/L1_Define_AI`
- Example index: `https://aliens.school/Course_ACLE/HIEN/AI/`

### [21.4] Description Rules
- Derived from `<title>` tag content
- HTML entities decoded (&#8212; → -, &#183; → -)
- Max 160 characters (truncate with `...`)
- Same text used for og:description and twitter:description

### [21.5] Placement
- SEO tags go AFTER `</title>` and BEFORE `<link rel="preconnect">`

---

## [22] JSON Index Files — Clean URL Update

### [22.1] Rule
JSON index files (`course-index.json`) me `"file"` values se `.html` extension REMOVE karna mandatory hai.

### [22.2] Examples
```json
// CORRECT (clean URL):
{"file": "L1_Welcome_to_the_Certificate", "name": "Welcome to the Certificate"}

// WRONG (old format):
{"file": "L1_Welcome_to_the_Certificate.html", "name": "Welcome to the Certificate"}
```

### [22.3] JS Engine Compatibility
`aliens-cinematic.js` ka `_detectIndexPath()` function ab `_currentFile` se `.html` strip karta hai —
isiliye JSON me bhi extensionless filenames MUST hain taaki matching kaam kare.

### [22.4] JSON Files Affected
| Project | JSON Name | Count |
|---------|-----------|-------|
| Course_ACLE | course-index.json | 44 |
| Skill_ACLE | course-index.json | 61 |
| Wiki_ACLE | (no JSON index) | 0 |
Total: **105 JSON files**

---

## [23] Updated Stats (2026-04-13)

| Project | HTML Files | Index Pages | JSON Index | Programs/Topics |
|---------|-----------|-------------|------------|----------------|
| Course_ACLE | 6,483 | 45 | 44 | 44 programs |
| Skill_ACLE | 2,599 | 62 | 61 | 61 topics |
| Wiki_ACLE | 2,870 | 5 | 0 | 4 letters (A-D) |
| **Total** | **11,952** | **112** | **105** | |

---

## [24] TTS (Text-to-Speech) Engine Architecture

### [24.1] Overview
ACLE ke sab converter scripts ek shared TTS module use karte hain:
`/Aliens/Cyborg/AlienCyborg/Code/acle_tts.py`

Dual-engine architecture:
- **Primary**: ElevenLabs (premium, human-like Hinglish voice via `eleven_multilingual_v2` model)
- **Fallback**: edge-tts (free Microsoft Neural TTS, `hi-IN-MadhurNeural` voice)

Per-file fallback: agar ElevenLabs kisi ek audio file par fail ho jaye, us file ke liye edge-tts
automatically take over karta hai — baaki files ElevenLabs se hi generate hoti hain.

### [24.2] Shared Module (SSOT)
```
/Aliens/Cyborg/AlienCyborg/Code/acle_tts.py
```
- ALL ACLE converters import karein: `from acle_tts import generate_audio_files`
- Per-converter inline edge-tts code **REMOVED** — sab kuch acle_tts.py me centralized hai
- Zero new dependencies — ElevenLabs API calls `urllib.request` (stdlib) se hoti hain

### [24.3] API Key Storage (Secure)
| Priority | Source | Path/Key |
|----------|--------|----------|
| 1 | Environment Variable | `ELEVENLABS_API_KEY` |
| 2 | Central Secret Repo (SSOT) | `/Aliens/Secret/elevenlabs_api_key.txt` |
| 3 | Developer Secrets File (legacy) | `/Aliens/.Alien/{Username}/Secrets/elevenlabs_api_key.txt` |

- Secrets folder `.gitignore` me protected hai (`*/Secrets/` pattern)
- API key kabhi bhi code me hardcode NAHI hogi
- Agar key unavailable ho → ElevenLabs disabled → automatic edge-tts fallback

### [24.4] Voice Configuration
| Setting | Value | Description |
|---------|-------|-------------|
| Model | `eleven_multilingual_v2` | Best Hinglish quality |
| Stability | 0.50 | Balance: expressive yet consistent |
| Similarity | 0.75 | Strong voice identity |
| Style | 0.45 | Natural emphasis/emotion |
| Speaker Boost | true | Enhanced clarity |
| Output Format | `mp3_44100_128` | 44.1kHz, 128kbps |
| Voice ID | Auto-detect or `ELEVENLABS_VOICE_ID` env var | First suitable multilingual voice |

Edge-TTS fallback:
| Setting | Value |
|---------|-------|
| Voice | `hi-IN-MadhurNeural` |
| Rate | `+0%` |
| Pitch | `+0Hz` |

### [24.5] Engine Selection
Environment variable `ACLE_TTS_ENGINE` controls engine selection:
| Value | Behavior |
|-------|----------|
| `auto` (default) | ElevenLabs if available, else edge-tts |
| `elevenlabs` | ElevenLabs only (fail if unavailable) |
| `edge-tts` | edge-tts only (skip ElevenLabs) |

### [24.6] Integration with Converters
| Converter | Import | Usage |
|-----------|--------|-------|
| `convert_lesson.py` | `from acle_tts import generate_audio_files` | Course_ACLE lessons |
| `convert_article.py` | `from acle_tts import generate_audio_files` | Wiki/Skill/Wikipedia articles |
| All wrappers (`convert_skill.py`, `convert_wikipedia.py`, etc.) | Via `convert_article` engine | Automatically use acle_tts |

### [24.7] Force Regeneration (Update Workflows)
- `regenerate_audio_files()` — deletes existing `slide_*.mp3` files, then generates fresh
- `batch_all_programs.py --force-audio` — re-generates audio for all already-converted lessons
- `batch_all_programs.py --force` — full re-convert (HTML + audio)
- Useful when switching from edge-tts to ElevenLabs (upgrade existing content)

### [24.8] Internet Requirement
- ElevenLabs requires internet connectivity + valid API key + sufficient quota
- edge-tts also requires internet (Microsoft's cloud TTS service)
- Audio generation completely offline nahi ho sakta — internet mandatory

### [24.9] Cost Estimation (ElevenLabs)
| Metric | Value |
|--------|-------|
| Per lesson avg chars | ~4,000 |
| Total Course_ACLE | ~26M chars (6,439 lessons) |
| Recommended Plan | Scale ($299/mo, 2M chars/mo) |
| Estimated Duration | ~9 months for full Course_ACLE |
| Cost per 1K chars | $0.10 (Scale plan) |

---

# END — ACC.ACLE.md
