---
RefId: WebApp.NamingAndScreens
Type: ReferenceRules
Domain: WebApps
Scope: Naming + Prefixing + Screen System
Status: Stable
Version: 26.00.00
LastUpdated: 2026-01-21
Purpose: >
  WebApps theme me Component/Section/Screen/Template naming + prefix rules.
  Ye SSOT hai taaki new laptop/new chat me rules repeat na hon.
NonNegotiables:
  - AppPrefixMandatory: true
  - DotPrefixMandatory: true
  - NoDoublePrefix: true
  - ComponentCssSuffix: ""
  - SectionCssSuffix: "-Section"
  - ScreenCssSuffix: "-Screen"
  - LayoutCssSuffix: "-Layout"
  - NoTemplatesForNewWork: true
  - ComponentsSectionsScreensHierarchy: true
  - HtmlSkeletonOnlyCustomTags: true
  - PluralLoopDelegatesToSingular: true
  - BackendGuardProcessing: true
...

# WebApp — Naming, Prefixing, and Screens (SSOT)

## [0] Core UI Hierarchy (SSOT)
WebApps ke andar UI artifact hierarchy fix hai:

1) **Components**
2) **Sections** (compose components)
3) **Screens** (compose sections)
4) **WebApp** (screens host)

Clarification (non-negotiable, practical reality):
- **Component** actual me multiple standard HTML elements se mil kar banta hai (div/span/button/etc.).
- **Section** actual me multiple components + minimal wrappers se banti hai (layout-only).
- **Screen** actual me multiple sections + minimal wrappers se banti hai (composition-only).
- **WebApp** actual me multiple screens ko host karta hai (routing/nav/wiring), screen ke andar ka UI structure nahi todta.

Important:
- New work me **Templates** ko primary artifact na samjho; Templates architecture outdated hai.
- Composer rule:
  - Section: **layout-only**, sirf components call kare
  - Screen: **composition-only**, sirf sections call kare
  - WebApp/page: sirf screens host kare

---

## [1] Universal Prefix Rule (AppName)
Theme me jo bhi cheez banegi, uska **prefix AppName** hoga:
- Component
- Section
- Screen
- Layout parts (in `User/layout/`)

Note:
- New work me **dot prefix** mandatory hai: `AppName.<Basename>`.
- Legacy underscore prefix (`AppName_<Basename>`) is no longer valid for new artifacts.

### 1.0 BaseName Prefix Rule (STRICT)
Rule:
- Agar target filename ka basename already `AppName.` se start hota hai to **achhi baat** — **dobara AppName prefix add nahi karna**.
- Agar basename me `AppName.` prefix missing ho to **prefix add karna mandatory**.

Prefix detection (deterministic):
- Prefix check **exact and case-sensitive** hai (App naming strict hoti hai).

Strictness (NON-NEGOTIABLE):
- Ye rule **strict** hai. Generate/Update me filename ka basename MUST follow normalized prefix output.
- Planner/CSV me `Name` (ya workflow ka raw input name) sirf input seed hai; final filename MUST be normalized form.
- Koi workflow ko allowed nahi ke wo `Name` ko ignore karke different filename base choose kare.

Example:
- AppName: `AlienCyborg`
- Valid names:
  - `AlienCyborg.Profile`
  - `AlienCyborg.Deposit`
  - `AlienCyborg.Deposit_Header`

### 1.1 No Double Prefix (Deterministic)
If user ne already full name diya ho (starts with `AppName.`) then:
- **dobara prefix mat lagana**.

Else:
- `NormalizedName = AppName + "." + RawName`

Normalization compatibility (strict, deterministic):
- If input starts with `AppName_` (legacy style), convert ONLY the first separator to dot:
  - `AppName_Rest` => `AppName.Rest`

### 1.2 Forbidden: Double BaseName
Forbidden examples:
- `B-Commerce.B-Commerce.Home.php`
- `B-Commerce.B-Commerce.User_ProfilePicture.css`

Allowed examples:
- `B-Commerce.Home.php`
- `B-Commerce.User_ProfilePicture.css`

### 1.2 Planner `Name` Column = File Basename Seed (STRICT)
Jab task `Worklflow` in groups me ho:
- `WebApp_Screen`
- `WebApp_Section`
- `WebApp_Component`

To planner CSV ka `Name` **free-text title** nahi hota.
Ye file basename seed hota hai jo normalize hoke filenames banta hai.

Rules:
- `Name` me spaces/marketing words/emoji/extra punctuation mat rakho.
- `Name` must be compatible with deterministic normalization rules.
- Output artifacts MUST use the normalized name in their filenames (PHP + CSS + docs paths).

Examples:
- App=`B-Commerce`, Name=`B-Commerce.Home` => Screen file: `B-Commerce.Home.php`
- App=`B-Commerce`, Name=`B-Commerce_Home` => Normalized: `B-Commerce.Home` (legacy input normalized)
- App=`B-Commerce`, Name=`Home` => Normalized: `B-Commerce.Home` (prefix auto)

---

## [2] Screen System (Template Replacement)
### 2.1 Screen = Sections Composition
Jis tarah `Template` sections se banta tha, **Screen** bhi sections se banta hai.

Observed pattern:
- Screen file has a semantic root tag (e.g. `<Home>`, `<Deposit>`, `<Deposits>`)
- Inside it, it calls multiple sections:
  - `Section('App_Screen_Part')`

### 2.2 Screen File Naming (PHP)
Rule:
- `{{AppName}}.{{ScreenName}}.php`

Location:
- `.../User/screen/`

### 2.3 Screen CSS Naming
Rule:
- `{{AppName}}.{{ScreenName}}-Screen.css`

Location:
- `.../User/css/`

Notes:
- Screen CSS usually scopes by a screen wrapper tag (example: `sdpt`, `sdss`), but exact wrapper tags are project-style driven.
- If wrapper tags are unclear, Planning must define them explicitly (no guessing).

---

## [3] Component Naming
### 3.1 Component PHP
- `{{AppName}}.{{ComponentName}}.php` (normalized)
- Location: `.../User/component/`

### 3.2 Component CSS
- `{{AppName}}.{{ComponentName}}.css` (same basename as component)
- Location: `.../User/css/`

Component examples (strict):
- App: `B-Commerce`
  - `B-Commerce.User_ProfilePicture.php`
  - `B-Commerce.User_CoverImage.php`
  - `B-Commerce.User_Addresses.php`
  - `B-Commerce.User_Address.php`
  - `B-Commerce.User_ProfilePicture.css`
  - `B-Commerce.User_CoverImage.css`
  - `B-Commerce.User_Addresses.css`
  - `B-Commerce.User_Address.css`

### 3.3 Component Types (Singular vs Plural) (SSOT)
- Component 2 types hotay hain:
  - **Singular** (leaf): per-item/unit UI
  - **Plural** (list): wrapper + loop orchestration
- Plural component hamesha ek singular child component se mil kar banta hai (plural -> singular delegation).
- Singular component ke andar kabhi bhi `Component()` call nahi hota.
- Section me singular + plural dono components call ho sakte hain (section is composition layer).

UTag note:
- UTag allocation + format SSOT: `Workflows/ACC/_Refs/WebApp_UTag.md`
- Legacy v1 examples: singular 4 letters (e.g., `prdt`), plural 5 letters ending `s` (e.g., `prdts`).
- New work uses v2 per SSOT, but plural tag still ends with `s`.

---

## [4] Section Naming
- `{{AppName}}.{{ScreenName}}_{{SectionName}}.php` (normalized)
- Location: `.../User/section/`

Section CSS (standard):
- `{{AppName}}.{{ScreenName}}_{{SectionName}}-Section.css`
- Location: `.../User/css/`

Section examples (strict):
- App: `B-Commerce`
  - `B-Commerce.Home_Header.php`
  - `B-Commerce.Home_Hero.php`

---

## [4.5] Layout Parts Naming
Layout parts are small PHP fragments included by `User/layout.php`.

Rule (layout part PHP):
- `{{AppName}}.{{LayoutPartName}}.php`

Location:
- `.../User/layout/`

Notes (non-negotiable):
- This rule applies to files inside `User/layout/`.
- Entry/aggregator files like `User/layout.php`, `User/css.php`, `User/js.php`, `User/component.php` are framework files and are not renamed by this rule.

Layout examples (strict):
- App: `B-Commerce`
  - `B-Commerce.Nav.php`
  - `B-Commerce.Header.php`
  - `B-Commerce.Footer.php`

### 4.6 Layout CSS Naming
If a layout-part has CSS (optional), it must follow type suffix naming.

Rule (layout CSS):
- `{{AppName}}.{{LayoutPartName}}-Layout.css`

Location:
- `.../User/css/`

Notes:
- Component CSS does NOT use a type suffix (it stays `{{Name}}.css`).

---

## [5] HTML Standardization (SSOT)
Component/Section/Screen markup me **custom HTML tags har jagah use mat karo**.

Allowed fixed skeleton custom tags (only):
- `<{UTAG}>` root
- `<bl>`
- `<hd>`
- `<bd>`
- `<ft>`

Skeleton ke andar:
- Use **standard HTML**: `div`, `span`, `p`, `ul`, `li`, `button`, `time`, `h1-h6`, etc.
- Styling ke liye **classes** use karo.

---

## [6] Plural Loop Rendering Rule (SSOT)
Plural components me loop ke andar heavy HTML print **kabhi** na karo.

Rule:
- Repeated row/item UI ke liye ek **Singular Component** banao/reuse karo.
- Loop me sirf wrapper + call:

  `Component('App_SingularComponent', $DataArray, true)`

Plural component ka kaam:
- list container (`ul/li`) + minimal wrappers
- data-to-item mapping

Preview rule (no hardcoded demo):
- Plural component by-default demo/static items **mat** rakho.
- Preview chahiye ho to caller `Component('X')` (no args) call kar sakta hai (Screen render me), aur component BackendGuard ke andar **default dataset** render kar sakta hai.
- Optional: caller explicit dummy/data pass karke preview force kar sakta hai.

---

## [7] BackendGuard Processing Rule (SSOT)
Performance + integration discipline:

- Kisi bhi component me zyada se zyada PHP processing **sirf** is condition ke andar honi chahiye:

  `global $Screen; if ( defined('Screen') && Screen && is_array($Array) && ( !empty($Array) || !empty($Screen) ) ) { ... }`

Guard ke andar hi:
- `$Data = array_merge($Default, $Array)`
- loops / list rendering
- heavy formatting
- plural components me singular component calls

Guard fail ho to:
- component minimal UI render kare
- `$Array` merge/loop na kare

Reason:
- Backend integration ho to hi processing run ho.
- Front preview/default UI ke liye component ko **no-args** call kiya ja sakta hai (e.g. `Component('X')`), aur component guard ke andar **default dataset** render kar sakta hai.

Dummy dataset policy (SSOT):
- Dummy/default dataset handling sirf BackendGuard ke andar ho.
- Explicit dummy: caller `$Array['dummy']=1` pass karke force enable kar sakta hai.
- Default dataset: jab component **no payload** ke sath Screen render me call ho (WebOS `Component()` global `$Screen=true` set karta hai), tab component default dataset render kar sakta hai.

---

## [8] CSS Selector System (SSOT)
Goal: CSS clean + stable rahe, aur component/section ka scope kabhi leak na ho.

### 8.1 Scope Rule (Non-negotiable)
- Component CSS hamesha component UTag se scoped ho (example: `prdt ...`).
- Section CSS hamesha Screen wrapper + semantic section tag se scoped ho (example: `prod header ...`, `prod aerea ...`).

### 8.2 Selector Depth Rule (Strict)
Selector depth count **spaces** se hota hai:
- Best: `{{selector1}} {{selector2}}`
- Max allowed: `{{selector1}} {{selector2}} {{selector3}}`

If 3 se zyada chain chahiye:
- extra wrapper chain add mat karo
- instead: markup me ek short class add karo (example: `a1`) aur usko target karo.

### 8.3 Class System Rule (Strict)
- Styling ke liye **extra-short** classes use karo:
  - `a1..a9`, `b1..b9` (and continue alphabet if needed)
- Descriptive/long classes (e.g. `Thumb`, `Title`, `AlienCyborg_Products_Header`) CSS contract ke liye **use mat karo**.
- `.{{AppName}}_*` classes ko CSS selector me depend mat karo (update me remove/replace with short classes).

### 8.4 CSS Formatting Rule (Strict)
- Har selector ka rule **1 line** me ho:
  - `selector{ prop:val; prop:val; }`
- Do selectors ko ek hi line me squash mat karo.
- Blank lines avoid karo.

`@media` blocks formatting:
- `@media(max-width:999px){`
- inner rules = one selector per line
- closing brace = `}` on its own line

---

## [9] UTag Global Uniqueness (SSOT)
UTag rule (non-negotiable):
- UTag is the unique HTML wrapper tag used for Screen/Section/Component scope.
- UTag collisions are forbidden across the entire WebOS ecosystem (all WebApps).
- Manual UTag selection is forbidden for new work.

Allocation + multi-PC sync SSOT:
- `Workflows/ACC/_Refs/WebApp_UTag.md`

