---
RefId: ACC.ATLE
Type: ACC.Reference
Scope: ATLE_Architecture
Status: Active
Version: 26.08.00
LastUpdated: 2026-04-13
Audience: "AlienCyborg Agent + ACC Workflow Authors"
Purpose: >
  ATLE (Aliens Text Learning Experience) ecosystem ki architecture, assets, HTML template rules,
  validation checklist, aur post-generation requirements define karna.
  Yeh reference ALL ATLE workflows (Course_ATLE, Skill_ATLE, Wiki_ATLE) ke liye SSOT hai.
NonNegotiables:
  - CentralAssetsOnly: true
  - NoLocalAssets: true
  - CleanURLs: true
  - NoHtmlExtension: true
  - NoIndexInHref: true
  - SEOMetaTags: true
  - RootHtaccess: true
  - CourseIndexJSON: true
  - ThemeToggleSupport: true
  - LessonNavigation: true
  - MarkdownFetch: true
  - MarkedJsCDN: true
---

# ACC.ATLE.md — ATLE Ecosystem Architecture Reference

## [01] Overview (Hinglish)
ATLE (Aliens Text Learning Experience) ek ecosystem hai jisme source content (Course, Skills, Wiki)
ko text-based reading shells me convert kiya jata hai. Har shell ek lightweight HTML page hai jo
markdown file ko runtime par `fetch()` + `marked.js` se render karta hai.

ATLE is ACLE ka text-based counterpart:
- **ACLE** = Cinematic Video Lessons (animated SVG, TTS audio, slides)
- **ATLE** = Text Reading Experience (markdown reader, TOC, code copy, course panel)

Is reference me:
- Central asset architecture (SSOT)
- Per-project asset path depth rules
- HTML shell template structure
- Clean URL rules (NON-NEGOTIABLE)
- SEO meta tag requirements
- JSON index files schema
- Post-generation validation checklist
- Index.html navigation system
- Theme toggle + keyboard shortcuts

> IMPORTANT: Yeh reference ALL ATLE workflows ko apply hoti hai.
> Koi bhi ATLE workflow run karne se pehle yeh ref read karna MANDATORY hai.

---

## [02] ATLE Ecosystem Map

### [02.1] Central Asset Repository (SSOT)
```
/Aliens/ATLE/assets/
    css/aliens-text.css       (text reader stylesheet — production, ~22KB)
    js/aliens-text.js         (text reader engine — production, ~23KB)
```

- ALL ATLE projects use assets from this ONE central location.
- Local `assets/` folder inside ANY *_ATLE repo is **FORBIDDEN** — must NOT exist.
- Assets update sirf `/Aliens/ATLE/assets/` me hoga, individual repos me kabhi nahi.
- Agar kisi *_ATLE repo me `assets/` folder mile, it's a legacy artifact — DELETE it.

### [02.2] External Dependencies
| Dependency | Source | Purpose |
|-----------|--------|---------|
| marked.js | `https://cdn.jsdelivr.net/npm/marked/marked.min.js` (CDN) | Markdown → HTML rendering |
| Inter font | Google Fonts | Body text |
| Space Grotesk | Google Fonts | Headings |

### [02.3] ATLE Consumer Projects
| # | Project | Source Repo | Output Repo | Source Format | Branding |
|---|---------|------------|-------------|---------------|----------|
| 1 | Course_ATLE | `/Aliens/Course/` | `/Aliens/Course_ATLE/` | `.md` | Course ATLE — Aliens School |
| 2 | Skill_ATLE | `/Aliens/Skills/` | `/Aliens/Skill_ATLE/` | `.md` | Skill ATLE — Aliens School |
| 3 | Wiki_ATLE | `/Aliens/Wiki/` | `/Aliens/Wiki_ATLE/` | `.md` | Wiki ATLE — Aliens School |

### [02.4] Master Hub
- `/Aliens/ATLE/index.html` — master hub linking to all 3 ATLE projects
- Dark/light theme UI with project cards, stats, gradient glow effects
- Stats: 19,633 total lessons, 3 learning paths, 235 topics & programs

---

## [03] Project Structures (Confirmed)

### [03.1] Course_ATLE
```
Course_ATLE/
    HIEN/
        {Program}/                          # e.g. Advanced_Data_Analytics
            index.html                      # program-level index (card grid)
            course-index.json               # lesson tree for Course Index Panel
            {Course}/                       # e.g. C1_Foundations_of_Data_Science
                {Module}/                   # e.g. M1_Introduction_to_Data_Science
                    {Lesson}.html           # e.g. L1_Welcome_to_the_Certificate.html
        {Program}.html                      # course landing page (lists courses)
```
- Depth from repo root: **5 levels** (HIEN/Program/Course/Module/Lesson.html)
- Lesson HTML is a **shell** — fetches markdown from `data-md` at runtime
- Stats (2026-04): 44 programs, ~346 courses, ~1,457 modules, 10,922 lesson shells

### [03.2] Skill_ATLE
```
Skill_ATLE/
    HIEN/
        {Topic}/                            # e.g. Python, Accountability
            index.html                      # topic-level index
            skill-index.json                # lesson list for Course Index Panel
            {Lesson}.html                   # e.g. 01_AC_Introduction.html
```
- Depth from repo root: **3 levels** (HIEN/Topic/Lesson.html)
- Stats (2026-04): 186 topics, 5,604 lesson shells

### [03.3] Wiki_ATLE
```
Wiki_ATLE/
    HIEN/
        {Section}/                          # e.g. A, B, C, D, _
            index.html                      # section-level index
            wiki-index.json                 # article list for Course Index Panel
            {Article}.html                  # e.g. Algorithm.html
```
- Depth from repo root: **3 levels** (HIEN/Section/Article.html)
- Stats (2026-04): 5 sections (_, A, B, C, D), 3,340 article shells

---

## [04] Asset Path Depth Rules (NON-NEGOTIABLE)

HTML file se central ATLE assets tak relative path calculate karna:
- File ka parent directory se workspace root (`/Aliens/`) tak `../` count karo
- Phir `ATLE/assets/` append karo

### Per-Project Asset Path Table (SSOT)
| Project | HTML Location (from repo root) | Levels to /Aliens/ | Correct Relative Path |
|---------|-------------------------------|--------------------|-----------------------|
| Course_ATLE (lesson) | `HIEN/{Prog}/{Course}/{Mod}/{Lesson}.html` | 5 | `../../../../../ATLE/assets/` |
| Course_ATLE (program index) | `HIEN/{Program}/index.html` | 2 | `../../ATLE/assets/` (inline CSS, no external) |
| Skill_ATLE | `HIEN/{Topic}/{Lesson}.html` | 3 | `../../../ATLE/assets/` |
| Wiki_ATLE | `HIEN/{Section}/{Article}.html` | 3 | `../../../ATLE/assets/` |

### Asset References in HTML Shell (Template)
```html
<!-- CSS -->
<link rel="stylesheet" href="{ASSET_PATH}css/aliens-text.css">

<!-- marked.js CDN (loaded BEFORE engine) -->
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>

<!-- ATLE Engine -->
<script src="{ASSET_PATH}js/aliens-text.js"></script>
```

### WRONG Path Examples (FORBIDDEN)
- `../assets/` — local assets reference (WRONG)
- `../../assets/` — local assets reference (WRONG)
- `./assets/` — local assets reference (WRONG)
- `assets/` — local assets reference (WRONG)
- Any path that does NOT contain `ATLE/assets/` is WRONG.

---

## [05] HTML Shell Template (Lesson Pages)

### [05.1] What is a "Shell"?
ATLE lesson pages are **empty shells** — they contain no content at generation time.
The shell has:
- `data-md` attribute on `<body>` — points to source markdown file
- `data-index` attribute on `<body>` — points to JSON index file
- ATLE JS engine fetches markdown at runtime, renders it with `marked.js`

### [05.2] Course_ATLE Lesson Shell Template
```html
<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>{Lesson Name} &#8212; Course ATLE &#8212; Aliens School</title>
<!-- SEO META TAGS (see Section [09]) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Space+Grotesk:wght@500;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="../../../../../ATLE/assets/css/aliens-text.css">
</head>
<body data-md="../../../../../Course/HIEN/{Program}/{Course}/{Module}/{Lesson}.md" data-index="../../course-index.json">
<!-- Background effects -->
<div class="bg-grid"></div>
<div class="bg-glow bg-glow-1"></div>
<div class="bg-glow bg-glow-2"></div>
<!-- Reading progress -->
<div id="reading-progress"><div class="bar"></div></div>
<!-- Top bar -->
<header id="atle-topbar">
    <div class="atle-brand">
        <span class="atle-logo">&#128125;</span>
        <span class="atle-title">ATLE</span>
        <span class="atle-sep">&#183;</span>
        <span class="atle-context">{Program Name}</span>
    </div>
    <div class="atle-tools">
        <button id="btn-index" class="atle-btn" title="Course Index (I)">&#128218;</button>
        <button id="btn-tools" class="atle-btn" title="Tools (])">&#128295;</button>
        <button id="btn-theme" class="atle-btn" title="Theme (T)">&#127769;</button>
        <button id="btn-shortcuts" class="atle-btn" title="Shortcuts (?)">&#9000;</button>
        <button id="btn-print" class="atle-btn" title="Print (P)">&#128424;</button>
        <button id="btn-pdf" class="atle-btn" title="PDF Export">&#128196;</button>
    </div>
</header>
<!-- Course Index Panel (left sidebar) -->
<aside id="course-panel">
    <div class="cp-header">
        <span class="cp-title">Course Index</span>
        <button class="cp-close" onclick="ATLE.toggleCoursePanel()">&#10005;</button>
    </div>
    <div class="cp-search"><span class="cp-search-icon">&#128269;</span><input type="text" placeholder="Search lessons..."></div>
    <div class="cp-tree"></div>
</aside>
<!-- Tools Panel (right sidebar) -->
<aside id="tools-panel"><!-- Tools content --></aside>
<!-- Main content area (markdown rendered here) -->
<main id="atle-main">
    <div class="atle-loading"><div class="spinner"></div><span>Loading lesson...</span></div>
    <div class="atle-error" style="display:none"><div class="atle-err-detail"></div></div>
</main>
<!-- Breadcrumb navigation -->
<nav class="atle-breadcrumb">
    <a href="../../../../../ATLE/">&#128125; ATLE</a>
    <span>&#8250;</span>
    <a href="../../../../../Course_ATLE/">Courses</a>
    <span>&#8250;</span>
    <a href="../../">{Program}</a>
</nav>
<!-- Lesson navigation (prev/next) -->
<nav id="lesson-nav"></nav>
<!-- Keyboard shortcuts overlay -->
<div id="kb-overlay"><!-- Shortcuts grid --></div>
<!-- Toast notifications -->
<div id="toast"></div>
<!-- Scripts -->
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="../../../../../ATLE/assets/js/aliens-text.js"></script>
</body>
</html>
```

### [05.3] data-md Paths (Source Markdown References)
| Project | data-md Pattern | Example |
|---------|----------------|---------|
| Course_ATLE | `../../../../../Course/HIEN/{Prog}/{Course}/{Mod}/{Lesson}.md` | `../../../../../Course/HIEN/AI/C1_Intro/M1_What/L1_Define.md` |
| Skill_ATLE | `../../../Skills/HIEN/{Topic}/{Lesson}.md` | `../../../Skills/HIEN/Python/01_Intro.md` |
| Wiki_ATLE | `../../../Wiki/HIEN/{Section}/{Article}.md` | `../../../Wiki/HIEN/A/Algorithm.md` |

### [05.4] data-index Paths (JSON Index References)
| Project | data-index Pattern | JSON Name |
|---------|-------------------|-----------|
| Course_ATLE | `../../course-index.json` | course-index.json |
| Skill_ATLE | `skill-index.json` | skill-index.json |
| Wiki_ATLE | `wiki-index.json` | wiki-index.json |

---

## [06] ATLE Engine Features (aliens-text.js)

### [06.1] Core Features
| Feature | Description |
|---------|-------------|
| Markdown Rendering | `fetch()` markdown file → `marked.parse()` → inject into `#atle-main` |
| Table of Contents | Auto-generated from H1/H2/H3 headings (3+ headings required) |
| Code Copy Buttons | "Copy" button on every `<pre>` code block |
| Reading Progress | Scroll-based progress bar at page top |
| Course Index Panel | Left sidebar with collapsible module tree (from JSON) |
| Lesson Navigation | Prev/Next lesson links + keyboard arrows |
| Theme Toggle | Dark/light mode with localStorage persistence (`aliens-theme` key) |
| Tools Panel | Right sidebar for additional tools |
| Keyboard Shortcuts | Full set of shortcuts (see [06.3]) |
| PDF Export | Via browser print dialog |
| Toast Notifications | Brief action feedback messages |

### [06.2] ATLE Namespace
- All functionality under global `ATLE` object
- Entry point: `ATLE.init()` called on `DOMContentLoaded`
- Methods: `fetchMarkdown()`, `renderMarkdown()`, `loadCourseIndex()`, `buildCourseTree()`,
  `findCurrentLesson()`, `getCurrentFile()`, `updateLessonNav()`, `toggleTheme()`, etc.

### [06.3] Keyboard Shortcuts
| Key | Action |
|-----|--------|
| `T` | Toggle dark/light theme |
| `I` | Toggle Course Index Panel |
| `]` | Toggle Tools Panel |
| `?` | Show keyboard shortcuts help |
| `←` | Go to previous lesson |
| `→` | Go to next lesson |
| `P` | Print page |
| `Escape` | Close all overlays |

### [06.4] getCurrentFile() — Clean URL Compatible
```javascript
ATLE.getCurrentFile = function () {
    var loc = window.location.pathname;
    var parts = loc.split('/');
    var file = parts[parts.length - 1] || null;
    // Strip .html for clean URL compatibility
    if (file && file.length > 5 && file.indexOf('.html') === file.length - 5) {
        file = file.substring(0, file.length - 5);
    }
    return file;
};
```
- Extracts filename from URL path
- Strips `.html` extension (for server clean URL mode)
- Matches against JSON index `"file"` values (which are also extensionless)

---

## [07] Clean URL System (NON-NEGOTIABLE)

### [07.1] Overview
Aliens School domain (`aliens.school`) par `.html` extension URL me dikhna **FORBIDDEN** hai.
Root `.htaccess` (located at `/Aliens/.htaccess`) handles:
- `.html` → extensionless URL (301 redirect)
- `/path/index` → `/path/` (301 redirect)
- Extensionless URL → internally serves `.html` file

### [07.2] href Rules (ALL ATLE HTML Files)
| Pattern | Correct | WRONG |
|---------|---------|-------|
| Link to project root | `href="../../../Course_ATLE/"` | `href="../../../Course_ATLE/index"` or `.../index.html` |
| Link to ATLE hub | `href="../../../ATLE/"` | `href="../../../ATLE/index"` or `.../index.html` |
| Link to lesson | `href="L1_Welcome"` | `href="L1_Welcome.html"` |
| Link to current dir | `href="./"` | `href="index"` or `href="index.html"` |
| Cross-module lesson | `href="../../C1_Foundations/M1_Intro/L1_Welcome"` | with `.html` |

### [07.3] Rules Summary
- Kisi bhi internal `href` me `.html` extension FORBIDDEN hai
- Kisi bhi `href` me `index` as filename FORBIDDEN hai — directory URL (`./` ya `../path/`) use karo
- External links (googleapis, CDN, etc.) ko touch mat karo
- `data-md` attribute me `.md` extension RAKHNI hai (yeh file reference hai, URL nahi)
- `data-index` attribute me `.json` extension RAKHNI hai
- CSS/JS asset references me `.css`/`.js` extension RAKHNI hai

### [07.4] Root .htaccess (SSOT Location)
- File: `/Aliens/.htaccess`
- This file handles ALL URL rewriting for aliens.school
- Per-project .htaccess files are NOT needed
- Features: HTTPS force, /path/index→/path/ redirect, .html strip, extensionless→.html serve,
  security headers, caching, gzip, .md files are NOT blocked (engine fetches them via AJAX)

---

## [08] SEO Meta Tags (NON-NEGOTIABLE)

### [08.1] Overview
Har generated ATLE HTML file me SEO meta tags MANDATORY hain — Google Search Console submission ke liye.

### [08.2] Required Tags
```html
<title>{Lesson Name} &#8212; Course ATLE &#8212; Aliens School</title>
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

### [08.3] Canonical URL Rules
- Domain: `https://aliens.school`
- Path: file path relative to `/Aliens/` root, with `.html` stripped
- Index pages: `/path/to/` (not `/path/to/index`)
- Example lesson: `https://aliens.school/Course_ATLE/HIEN/AI/C1_Intro/M1_What/L1_Define_AI`
- Example index: `https://aliens.school/Course_ATLE/HIEN/AI/`

### [08.4] Description Rules
- Derived from `<title>` tag content
- HTML entities decoded (&#8212; → -, &#183; → -)
- Max 160 characters (truncate with `...`)
- Same text used for og:description and twitter:description

### [08.5] Placement
- SEO tags go AFTER `</title>` and BEFORE `<link rel="preconnect">`

---

## [09] JSON Index Files

### [09.1] Purpose
JSON files power the Course Index Panel (left sidebar) and lesson navigation (prev/next).
ATLE JS engine loads these via `fetch()` at runtime.

### [09.2] JSON Schema — Course type (nested hierarchy)
```json
{
  "type": "course",
  "name": "Advanced Data Analytics",
  "modules": [
    {
      "code": "C1_Foundations_of_Data_Science",
      "name": "C1 Foundations of Data Science",
      "submodules": [
        {
          "code": "M1_Introduction_to_Data_Science",
          "name": "M1 Introduction to Data Science",
          "lessons": [
            { "file": "L1_Welcome_to_the_Certificate", "name": "Welcome to the Certificate" }
          ]
        }
      ]
    }
  ]
}
```

### [09.3] JSON Schema — Skill type (nested but flat lessons)
```json
{
  "type": "skill",
  "name": "Accountability",
  "modules": [
    {
      "code": "Lessons",
      "name": "Accountability",
      "submodules": [
        {
          "code": "All",
          "name": "All Lessons",
          "lessons": [
            { "file": "00_INDEX", "name": "INDEX" },
            { "file": "01_AC_Introduction", "name": "AC Introduction" }
          ]
        }
      ]
    }
  ]
}
```

### [09.4] JSON Schema — Wiki type (flat articles)
```json
{
  "type": "wiki",
  "name": "Section A",
  "modules": [
    {
      "code": "Articles",
      "name": "Section A",
      "submodules": [
        {
          "code": "A",
          "name": "A (378)",
          "lessons": [
            { "file": "A_B_test", "name": "A B test" },
            { "file": "Algorithm", "name": "Algorithm" }
          ]
        }
      ]
    }
  ]
}
```

### [09.5] CRITICAL: Clean URL in JSON
- `"file"` values MUST be **extensionless** (no `.html`)
- JS engine `getCurrentFile()` strips `.html` from URL → matches extensionless JSON entries
- WRONG: `"file": "L1_Welcome.html"` → RIGHT: `"file": "L1_Welcome"`

### [09.6] JSON File Locations & Counts
| Project | JSON Path | JSON Name | Count |
|---------|-----------|-----------|-------|
| Course_ATLE | `HIEN/{Program}/course-index.json` | course-index.json | 44 |
| Skill_ATLE | `HIEN/{Topic}/skill-index.json` | skill-index.json | 186 |
| Wiki_ATLE | `HIEN/{Section}/wiki-index.json` | wiki-index.json | 5 |
| **Total** | | | **235** |

### [09.7] JSON Generation
- Generated via script scanning folder structure
- Course: scans C#_ > M#_ > L#_.html hierarchy, creates nested modules/submodules/lessons
- Skill: scans numbered .html files in flat folder, wraps in modules/submodules structure
- Wiki: scans article .html files in flat folder, wraps in modules/submodules structure
- Regeneration safe (overwrite allowed)
- MUST regenerate after new lessons are added

---

## [10] Index.html Navigation System

### [10.1] Purpose
Har ATLE project ko browseable navigation chahiye — users topics/programs/lessons discover kar sakein.

### [10.2] Project Root Index
- File: `/Aliens/{Repo}/index.html`
- Content: Dark/light theme card grid listing all topics/programs/sections
- Features: Search/filter bar, responsive grid, breadcrumbs, per-card stats, hover effects

### [10.3] Topic-Level Indexes
- File: `/Aliens/{Repo}/HIEN/{Topic_or_Program_or_Section}/index.html`
- Content: All lessons under that topic with direct links
- Features: Search bar, breadcrumbs, direct lesson links

### [10.4] Index Style Rules
- Background: Uses CSS variables (dark `#0a0e17`, light `#f8fafc`)
- Primary colors: `#22c55e` (green), `#0ea5e9` (blue)
- No external CSS dependency — CSS **inline** in index files
- Responsive: mobile + desktop
- Minimal JavaScript — pure CSS where possible, inline JS only if needed

### [10.5] Index Page Counts (2026-04)
| Project | Root Index | Topic/Program Indexes | Total |
|---------|-----------|----------------------|-------|
| Course_ATLE | 1 | 44 | 45 |
| Skill_ATLE | 1 | 186 | 187 |
| Wiki_ATLE | 1 | 5 | 6 |
| ATLE Hub | 1 | — | 1 |
| **Total** | | | **239** |

---

## [11] Dark/Light Theme Toggle System

### [11.1] Overview
ATLE reader supports dark (default) and light theme via `body.light-theme` CSS class.
Theme persists across pages via `localStorage` key `aliens-theme`.

### [11.2] Implementation
- `ATLE.toggleTheme()` — toggles theme, saves to localStorage, shows toast
- `ATLE.loadTheme()` — loads saved theme on page init
- Keyboard shortcut: `T` key
- All index pages have inline theme toggle button + light-theme CSS overrides

### [11.3] Integration Rules
- New CSS components MUST include `body.light-theme` overrides
- localStorage key `aliens-theme` is shared across ATLE + ACLE lesson and index pages
- Theme toggle button in index pages: `.theme-toggle` (fixed top-right, z-index 9000)

---

## [12] Post-Generation Validation Checklist (MANDATORY)

After EVERY batch generation, yeh checks run karo:

### [12.1] Asset Path Check
```powershell
$old = 0; $enc = New-Object System.Text.UTF8Encoding($false)
$files = [System.IO.Directory]::GetFiles('{RepoPath}/HIEN', '*.html', [System.IO.SearchOption]::AllDirectories)
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f, $enc)
    if($c.Contains('../assets/') -or ($c.Contains('assets/css/') -and -not $c.Contains('ATLE/assets/'))) { $old++ }
}
Write-Host "Local asset refs: $old"  # MUST be 0
```

### [12.2] Clean URL Check
```powershell
$bad = 0
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f, $enc)
    $m = [regex]::Matches($c, 'href="(?!https?://)[^"]*\.html"')
    $bad += $m.Count
}
Write-Host "Old .html hrefs: $bad"  # MUST be 0
```

### [12.3] Index href Check
```powershell
$bad = 0
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f, $enc)
    if($c -match 'href="[^"]*?/index"' -or $c -match 'href="index"') { $bad++ }
}
Write-Host "Index hrefs: $bad"  # MUST be 0
```

### [12.4] SEO Tags Check
```powershell
$missing = 0
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f, $enc)
    if($c -notmatch '<meta\s+name="description"') { $missing++ }
}
Write-Host "Missing SEO: $missing"  # MUST be 0
```

### [12.5] JSON Clean URL Check
```powershell
$bad = 0
$jsonFiles = [System.IO.Directory]::GetFiles('{RepoPath}/HIEN', '*-index.json', [System.IO.SearchOption]::AllDirectories)
foreach($jf in $jsonFiles) {
    $jc = [System.IO.File]::ReadAllText($jf, $enc)
    $bad += [regex]::Matches($jc, '"file":"[^"]*\.html"').Count
}
Write-Host "JSON old .html: $bad"  # MUST be 0
```

### [12.6] data-md Check
```powershell
# Verify data-md points to source repo (not ATLE repo)
$wrong = 0
foreach($f in $files) {
    $c = [System.IO.File]::ReadAllText($f, $enc)
    $m = [regex]::Match($c, 'data-md="([^"]*)"')
    if($m.Success -and -not $m.Groups[1].Value.Contains('/Course/') -and -not $m.Groups[1].Value.Contains('/Skills/') -and -not $m.Groups[1].Value.Contains('/Wiki/')) {
        $wrong++
    }
}
Write-Host "Wrong data-md: $wrong"  # MUST be 0
```

### [12.7] Summary Gates
| Check | Expected | Action if Failed |
|-------|----------|-----------------|
| Local asset refs | 0 | Bulk fix paths to central ATLE |
| .html in hrefs | 0 | Bulk strip .html from hrefs |
| index in hrefs | 0 | Bulk replace with directory URLs |
| Missing SEO | 0 | Bulk add SEO tags |
| JSON .html refs | 0 | Bulk strip .html from JSON |
| Wrong data-md | 0 | Fix source repo path references |
| Local assets/ folder | Not exists | Delete folder |

---

## [13] Comparison: ACLE vs ATLE

| Aspect | ACLE | ATLE |
|--------|------|------|
| Type | Cinematic Video Lessons | Text Reading Experience |
| Engine | `aliens-cinematic.js` | `aliens-text.js` |
| CSS | `aliens-cinematic.css` | `aliens-text.css` |
| Content Source | Pre-rendered (embedded in HTML) | Runtime fetch (markdown via `data-md`) |
| Audio | TTS mp3 per slide | None |
| Narrations | `_narrations.json` per lesson | None |
| Slides | Animated SVG slides | Markdown rendered text |
| File Size | 50KB-500KB per lesson | 3KB-8KB per lesson (shell only) |
| Runtime Dependencies | None (self-contained) | `marked.js` CDN |
| Central Assets | `/Aliens/ACLE/assets/` | `/Aliens/ATLE/assets/` |
| Theme | Dark/light (shared localStorage key) | Dark/light (shared localStorage key) |
| Navigation | Course Index Panel + Prev/Next | Course Index Panel + Prev/Next |
| Clean URLs | Yes (same .htaccess) | Yes (same .htaccess) |
| SEO | Yes | Yes |
| Hub | `/Aliens/ACLE/index.html` | `/Aliens/ATLE/index.html` |

---

## [14] PowerShell Terminal Rules (ATLE specific)

### [14.1] Remove-Item Blocked
- `Remove-Item` may be blocked by security policy
- Alternative: `[System.IO.Directory]::Delete($path, $true)` for folders
- Alternative: `[System.IO.File]::Delete($path)` for files

### [14.2] File Operations
- Read: `[System.IO.File]::ReadAllText($path, $enc)`
- Write: `[System.IO.File]::WriteAllText($path, $content, $enc)`
- UTF-8 without BOM: `$enc = New-Object System.Text.UTF8Encoding($false)`

### [14.3] Bulk Operations
- For bulk file operations (1000+ files), write `.ps1` script and execute with `powershell -ExecutionPolicy Bypass -File`
- Use `[System.IO.Directory]::GetFiles($path, '*.html', [System.IO.SearchOption]::AllDirectories)`
- Avoid `Get-Content`/`Set-Content` for large batches

---

## [15] Updated Stats (2026-04-13)

| Project | HTML Files | Index Pages | JSON Index | Programs/Topics |
|---------|-----------|-------------|------------|----------------|
| Course_ATLE | 10,922 | 45 | 44 | 44 programs |
| Skill_ATLE | 5,604 | 187 | 186 | 186 topics |
| Wiki_ATLE | 3,340 | 6 | 5 | 5 sections |
| ATLE Hub | 1 | 1 | — | — |
| **Total** | **19,867** | **239** | **235** | |

---

# END — ACC.ATLE.md
