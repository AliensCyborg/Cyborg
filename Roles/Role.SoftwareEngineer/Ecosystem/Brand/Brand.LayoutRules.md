# Brand.LayoutRules — Detailed Version

This document defines the **layout rules for Brands** inside the Aliens WebOS ecosystem, specifically for:

`Cyborg/AlienCyborg/Roles/Role.SoftwareEngineer/Ecosystem/Brand/Brand.LayoutRules.md`

Yeh file AlienCyborg (SoftwareEngineer role) ko yeh samjhati hai ki **Brand level par layout ka control kaise hota hai**, taaki:

- Har App ka UI structure predictable ho  
- Multi-Brand + Multi-App environment me bhi layout consistent rahe  
- Brand change karne se layout break na ho, sirf look & feel change ho  

---

## 1. Layout Responsibility — Brand vs WebOS vs App

### 1.1 WebOS Responsibility
WebOS:

- Core layout engine deta hai
- Base HTML skeleton, routing, theme loading, and system wrappers handle karta hai
- Standard regions define karta hai:
  - Header
  - Sidebar
  - Topbar
  - Content
  - Footer

Iska goal: **Structure + Behaviour**.

### 1.2 Brand Responsibility
Brand:

- In regions ka **visual style + structural arrangement** decide karta hai
- Define karta hai:
  - Header ka exact layout
  - Sidebar ka width, collapse pattern, icon-only mode
  - Content area ka padding, container width, background style
  - Footer ka look & presence

Iska goal: **Identity + Visual Grammar**.

### 1.3 App Responsibility
App:

- Sirf **content** ke upar focus karega:
  - Screens
  - Widgets
  - Tables, Forms, Cards, Charts
- App **Brand + WebOS layout** ka respect karega, override nahi karega.

Iska goal: **Business Logic + Screens**.

---

## 2. Brand Layout Layers

Each Brand ke liye layout ko 4 primary layers me socha jata hai:

1. **Shell Layout**
2. **Navigation Layout**
3. **Content Layout**
4. **Utility Layout**

### 2.1 Shell Layout

Shell = Full page structure.

Typical PHP/HTML include files:

- `Brand/{BrandCode}/Layout/header.php`
- `Brand/{BrandCode}/Layout/sidebar.php`
- `Brand/{BrandCode}/Layout/topbar.php` (optional)
- `Brand/{BrandCode}/Layout/footer.php`
- `Brand/{BrandCode}/Layout/main.php` (page wrapper, optional)

Shell layout ke rules:

- Consistent across all apps using this brand
- Responsive behaviour wahi rahega (sirf styles change ho sakte hain)
- No business logic inside layout files — sirf UI structure + includes

### 2.2 Navigation Layout

Navigation includes:

- Main sidebar
- Top navigation (if present)
- Breadcrumbs region

Brand define karta hai:

- Sidebar left/right
- Sidebar width (e.g. 240px, 280px)
- Collapse behaviour:
  - Hover expanded
  - Click to pin
- Icon + Label alignment
- Breadcrumbs ka position (header ke andar ya content ke top par)

### 2.3 Content Layout

Content layout controls:

- Max-width (fluid vs fixed)
- Content padding (top/bottom/left/right)
- Page title + action bar placement
- Section spacing
- Card/grid alignment

Brand yeh decide karega:

- Boxed layout vs full-width layout
- Background color (solid / gradient / subtle pattern)
- Card-based vs flat layout defaults

### 2.4 Utility Layout

Utility regions:

- Notifications container (toast location)
- Overlay layers (modals, drawers)
- Global search bar placement
- Floating action buttons (if used)

Brand in regions ko visually standard banata hai.

---

## 3. Standard Layout Skeleton

### 3.1 HTML Structure Pattern (Conceptual)

```html
<body class="brand-{BrandCode}">
  <div class="webos-shell">

    <?php include BrandLayout('header'); ?>

    <div class="webos-body">

      <?php include BrandLayout('sidebar'); ?>

      <main class="webos-content">
        <?php include BrandLayout('topbar'); ?>

        <section class="webos-page">
          <!-- Page Title + Actions -->
          <!-- Breadcrumbs -->
          <!-- Page Content (App renders here) -->
        </section>
      </main>
    </div>

    <?php include BrandLayout('footer'); ?>
  </div>
</body>
```

Yaha:

- `BrandLayout('header')` etc. ek helper concept hai (System/Theme ya Brand engine implement karta hai)
- App ke views sirf `webos-page` ke andar inject hote hain

### 3.2 Layout Helper Guidelines

- Layout helpers **Brand-specific** mapping ko handle karein
- Apps ko direct `include Brand/{BrandCode}/Layout/*.php` nahi karna chahiye
- Aise helpers AI + dev dono ke liye safe abstraction provide karte hain

---

## 4. Header Layout Rules

Header must:

- Show Brand + App identity:
  - Logo
  - App name (short)
- Show User identity:
  - Avatar / initials
  - Name
  - Quick profile dropdown

Brand defines:

- Height (fixed across all apps)
- Left/right sections distribution
- Optional elements:
  - Global search
  - Notification icon
  - Language switcher
  - Theme switch (light/dark)

**Rule:** Header clutter-free hona chahiye — 5–7 se zyada primary actions nahi.

---

## 5. Sidebar Layout Rules

Sidebar is often **core navigation hub**.

Brand config includes:

- Position: left (default) / right (rare)
- Expand/collapse behaviour
- Icon size + label font
- Grouped menu sections style (headers, dividers)
- Active state styling

For multi-brand scenario:

- Brand may switch from vertical sidebar to compact icon-only sidebar
- App logic must not break; only layout/visual variation honi chahiye

**Rule:** Menu structure DB/JSON se driven, layout sirf presentation handle kare.

---

## 6. Topbar / Secondary Navigation

Topbar optional layer hai jo:

- Filter controls
- Secondary navigation tabs
- Contextual controls (e.g. “Workspace switcher”, “Team selection”)
- Quick metrics (e.g. balance info)

Brand decides:
- Positioning (inside content vs below header)
- Style (tabs, pills, flat buttons)

**Guideline:** Topbar context-specific ho, global items header me hi rahen.

---

## 7. Content Layout Rules

### 7.1 Page Wrapper

Every page content must live inside a consistent wrapper:

- Title area
- Description / helper text
- Action buttons (Add, Export, etc.)
- Filters bar
- Main content

Brand ensures:

- Title size + alignment
- Action button placement (mostly right-aligned on desktop)
- Mobile responsiveness (stacked layout)

### 7.2 Section Layout

Sections inside content area:

- Cards
- Tables
- Forms
- Charts

Brand define karega:

- Section spacing (gap between cards/blocks)
- Background (plain vs elevated)
- Shadow/border pattern

**Rule:** Sections visually separated but not noisy.

---

## 8. Footer Layout Rules

Footer may contain:

- Version info (WebOS + App + Git sha)
- Copyright
- Links (Terms, Privacy)

Brand controls:

- Presence (footer optional in authenticated screens)
- Style
- Height

**Security Note:** No sensitive environment info show in footer in production.

---

## 9. Mobile/Layout Responsiveness Rules

Brand layout must:

- Collapse sidebar into drawer on small screens
- Reflow header contents:
  - Logo left
  - Hamburger menu
  - Essential icons only
- Content full-width on mobile
- Reduce padding where needed but keep readability

Breakpoints typically defined in Brand CSS/Theme:

- `sm`, `md`, `lg`, `xl` scale consistent across all apps

---

## 10. Theme + Layout Interaction

Brand Theme (Theme.json) + Layout:

- Theme config influences layout classes:
  - Border radius (sharp vs rounded)
  - Elevation levels
  - Grid spacing

**For AlienCyborg:**  
Jab bhi layout-related code generate karna ho, Theme + Brand layout dono ka respect karna mandatory hai.

---

## 11. Layout Extension Points (For AI & Devs)

Brand layout anek extension points provide karega:

- Hooks inside header:
  - `before-header-actions`
  - `after-header-actions`
- Hooks inside sidebar:
  - `before-menu`
  - `after-menu`
- Hooks around content:
  - `before-page-title`
  - `after-page-title`
  - `before-main-content`
  - `after-main-content`

Yeh hooks future me `WebOS.Hooks` engine ke saath integrate kiye ja sakte hain, taki:

- Without editing layout files, new things inject kiye ja saken.

---

## 12. Safety Rules for Layout Changes

1. **Never hard-code tenant-specific or app-specific logic inside Brand layout.**  
2. **No DB queries directly from layout files.**  
   - Use System/App engines to fetch data; layout sirf render kare.
3. **Avoid inline CSS** in layout files (use Brand assets CSS).
4. **Always test on:**
   - Desktop
   - Tablet
   - Mobile
5. **Never remove mandatory regions** (header, content root).

---

## 13. AI Usage Guidelines (For AlienCyborg)

When AlienCyborg edits/creates Brand layout:

- Always:
  - Respect existing structure
  - Reuse includes (`header.php`, `sidebar.php`, etc.)
  - Follow AliensStyle coding style and naming
- Prefer:
  - New partials/components over editing heavy core layout directly
- Avoid:
  - Breaking compatibility with existing Apps
  - Moving business logic into layout

Goal: **Safe, structured evolution of Brand layouts**.

---

## 14. Summary

`Brand.LayoutRules`:

- Defines how Brand controls the layout shell for all Apps  
- Keeps WebOS → Brand → App responsibility clean  
- Ensures that:

  - Brand switching is safe
  - Layout is predictable
  - AI + humans both can upgrade UI without fear

For AlienCyborg (SoftwareEngineer role), this file works as the **layout rulebook** for every brand-related design/system decision.
