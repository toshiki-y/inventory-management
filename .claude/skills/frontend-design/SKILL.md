---
name: frontend-design
description: Design system and layout conventions for the Vue 3 client, covering CSS design tokens, the left sidebar app shell, and the shared card, table, and badge primitives. Use this skill when creating or restyling any .vue file in client/src, or when editing the global style block in client/src/App.vue.
---

# Frontend Design System

This skill defines the visual language and layout shell for the Vue 3 client of the Factory Inventory Management System. It is the codified form of the instruction in [vue-expert](.claude/agents/vue-expert.md) that says "check existing styles in `App.vue` and match them."

## Where Styles Live

**There is exactly one global stylesheet.** Know this before editing anything.

```
client/src/App.vue:164-486    <style> with NO scoped attribute — 322 lines, the whole design system
client/src/**/*.vue           all 15 other components use <style scoped>
client/src/views/Backlog.vue  no <style> block at all, and not registered in the router
```

There is no `.css` file anywhere in `client/`, no `src/assets/`, no stylesheet import in `main.js`. Dependencies are only `vue`, `vue-router`, `axios` — no Tailwind, no UI library. **Never add a `.css` file and never import a stylesheet in `main.js`.** Global primitives go in `App.vue`; everything else goes in that component's scoped block.

### Never style a child component's internals from App.vue

`App.vue`'s style block is unscoped, so a selector like `.sidebar-footer .language-button` computes to specificity (0,2,0) — **exactly tying** `LanguageSwitcher`'s own scoped `.language-button[data-v-xxx]`, which is also (0,2,0). The winner is then decided by stylesheet source order, which is **not stable between HMR and a production build**. The style works in `npm run dev` and flips in `npm run build`.

```css
/* WRONG — in App.vue. Ties with the component's own scoped rule. */
.sidebar-footer .language-button { width: 100%; }

/* RIGHT — in App.vue. Targets only the component's root element. */
.sidebar-footer > * { width: 100%; }

/* RIGHT — the button treatment goes in LanguageSwitcher.vue's own scoped block. */
.language-button { width: 100%; justify-content: space-between; }
```

`.sidebar-footer > *` is safe because the child component's root element carries `.language-switcher` / `.profile-menu` and lives in `App.vue`'s own DOM.

### Do not rename or remove shared global classes

These are consumed by views that do not define them. Renaming one breaks pages silently.

```
.page-header  .page-header h2  .page-header p      (8 files)
.card  .card-header  .card-title                   the dominant panel primitive
.stats-grid  .stat-card  .stat-label  .stat-value   the stat-tile primitive
.badge + 10 modifiers                               every view
.table-container                                    (11 uses)
.loading  .error                                    every view
```

`views/Backlog.vue` is the reason this rule is absolute: it has **no styles of its own and is not in the router**, so it renders purely on global classes and **cannot be visually verified**. Only the nav-local classes are safe to delete: `.top-nav`, `.nav-container`, `.nav-container > *`, `.nav-tabs`, `.nav-tabs a`, `.logo`, `.subtitle`.

## Design Tokens

**Add this to the top of `App.vue`'s `<style>`, immediately before the `*` reset.** Values are extracted from the existing CSS — do not invent new colors.

```css
:root {
  /* Text — #0f172a wins over #1e293b (51 uses vs 1; the stray is on `body`) */
  --color-text:           #0f172a;
  --color-text-body:      #334155;
  --color-text-secondary: #475569;
  --color-text-muted:     #64748b;
  --color-text-subtle:    #94a3b8;
  --color-text-inverse:   #ffffff;

  /* Surfaces */
  --color-surface:        #ffffff;
  --color-surface-sunken: #f8fafc;
  --color-surface-hover:  #f1f5f9;
  --color-bg:             #f8fafc;

  /* Borders */
  --color-border:         #e2e8f0;
  --color-border-strong:  #cbd5e1;
  --color-border-subtle:  #f1f5f9;

  /* Primary — interaction blue is #2563eb. #3b82f6 survives only as --chart-1. */
  --color-primary:            #2563eb;
  --color-primary-hover:      #1d4ed8;
  --color-primary-strong:     #1e40af;
  --color-primary-soft:       #eff6ff;
  --color-primary-soft-strong:#dbeafe;
  --color-focus-ring:         rgba(37, 99, 235, 0.12);

  /* Status — one text-safe value plus a tint pair each */
  --color-success:         #059669;
  --color-success-soft:    #d1fae5;
  --color-on-success-soft: #065f46;
  --color-warning:         #ea580c;
  --color-warning-soft:    #fed7aa;
  --color-on-warning-soft: #92400e;
  --color-danger:          #dc2626;
  --color-danger-strong:   #991b1b;
  --color-danger-soft:     #fef2f2;
  --color-danger-border:   #fecaca;
  --color-info:            var(--color-primary);
  --color-info-soft:       var(--color-primary-soft-strong);
  --color-on-info-soft:    var(--color-primary-strong);

  /* Chart ramp — SVG fills and strokes only, NEVER text */
  --chart-1:       #3b82f6;
  --chart-2:       #10b981;
  --chart-3:       #f59e0b;
  --chart-4:       #8b5cf6;
  --chart-5:       #ef4444;
  --chart-neutral: #cbd5e1;

  /* Sidebar — its own group so a dark sidebar stays a localized change */
  --sidebar-width:           260px;
  --sidebar-width-collapsed: 72px;  /* deliberately unused — see Collapsible Sidebar */
  --color-sidebar-bg:              var(--color-surface);
  --color-sidebar-border:          var(--color-border);
  --color-sidebar-item:            var(--color-text-muted);
  --color-sidebar-item-hover-bg:   var(--color-surface-hover);
  --color-sidebar-item-active:     var(--color-primary);
  --color-sidebar-item-active-bg:  var(--color-primary-soft);

  /* Spacing — 4px base */
  --space-1: 0.25rem;  --space-2: 0.5rem;   --space-3: 0.75rem;
  --space-4: 1rem;     --space-5: 1.25rem;  --space-6: 1.5rem;
  --space-8: 2rem;     --space-10: 2.5rem;  --space-12: 3rem;

  /* Type */
  --text-xs:  0.75rem;   --text-sm: 0.875rem;  --text-base: 1rem;
  --text-lg:  1.125rem;  --text-xl: 1.375rem;  --text-2xl:  1.875rem;
  --text-3xl: 2.25rem;

  /* Radius */
  --radius-sm: 6px;   --radius-md: 8px;   --radius-lg: 10px;
  --radius-xl: 12px;  --radius-pill: 9999px;

  /* Shadows — slate-tinted, not pure black. This is a deliberate change from
     the current rgba(0,0,0,…) values; do not "correct" it back. */
  --shadow-xs: 0 1px 2px  rgba(15, 23, 42, 0.04);
  --shadow-sm: 0 1px 3px  rgba(15, 23, 42, 0.06);
  --shadow-md: 0 4px 12px rgba(15, 23, 42, 0.06);
  --shadow-lg: 0 10px 25px rgba(15, 23, 42, 0.10);
  --shadow-xl: 0 20px 40px rgba(15, 23, 42, 0.15);

  /* Motion */
  --transition:      0.2s ease;
  --transition-fast: 0.15s ease;

  /* Layout */
  --content-max: 1600px;

  /* Layering */
  --z-raised:     10;
  --z-sticky:     90;
  --z-app-chrome: 200;
  --z-dropdown:   1000;
  --z-modal:      2000;
}
```

### Reconciled duplicates

The app uses two blues and two reds interchangeably. **Split them by role rather than collapsing them.**

- **Blue.** `#2563eb` is the interaction color and clears 4.5:1 on white. `#3b82f6` is about 3.1:1 and **fails for text**. Its uses are almost all fills. So interaction → `--color-primary`, fills → `--chart-1`. The one value that genuinely changes is `FilterBar`'s focus border (`#3b82f6` → `#2563eb`); that is intentional and matches the focus ring.
- **Red.** `#dc2626` is text and interaction (`.stat-card.danger`, logout item, negative change). `#ef4444` is fills. Same split: `--color-danger` / `--chart-5`.
- **Body color.** `#1e293b` appears exactly once, on `body`, while every heading near it uses `#0f172a`. That is a stray, not a hierarchy. Collapse to `--color-text`.
- **Type.** `0.813rem` and `0.938rem` are on no scale — they are one-off nudges. Snapping them to `--text-xs` / `--text-sm` collapses five near-identical body sizes into two. **This is the cheapest single polish win in the whole redesign.**

### Scope of the replacement pass

There are 464 hex occurrences across 50 distinct values. **Mandatory replacement covers only the semantic set** — text, surface, border, primary, danger, success, warning. That is most of the occurrences and is concentrated in `App.vue` plus the handful of files a layout change already touches.

Chart-series hexes get `--chart-*` tokens defined now and substituted **opportunistically, when that chart file is next edited**. Tokenizing them buys no consistency — each is already single-purpose — and doing it in the same commit as an app-shell rewrite makes the diff unreviewable. If the layout regresses you will not be able to tell a bad grid from a bad color swap.

After this, **hardcoded hex in a `.vue` file is a defect.** Reconcile a stray value to the nearest token instead of adding a new variable.

### `var()` does not work in `@media`

Custom properties resolve per element; media queries are evaluated before that. Breakpoints are therefore **documented constants, not tokens**.

```css
/* WRONG — silently never matches */
@media (max-width: var(--bp-lg)) { }

/* RIGHT */
@media (max-width: 1280px) { }
```

`var()` does work in inline `:style` bindings, so chart colors bound in templates can migrate to `var(--chart-3)`.

## Application Shell

`.app` is currently a flex column stacking a header over the content. **Replace it with a two-column grid.**

```css
.app {
  display: grid;
  grid-template-columns: var(--sidebar-width) minmax(0, 1fr);
  grid-template-areas: "sidebar main";
  min-height: 100vh;
}

.app-main {
  grid-area: main;
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.main-content {
  max-width: var(--content-max);
  padding: var(--space-5) var(--space-6);
  min-width: 0;
}
```

**`minmax(0, 1fr)` is the single most load-bearing declaration in this file.** A bare `1fr` means `minmax(auto, 1fr)`, so a fixed-width descendant — the 200px donuts, the no-wrap filter row — pushes the whole track past the viewport and produces **page-level horizontal scroll** instead of being contained. `min-width: 0` on the flex child does the same job one level down. Both are required; neither substitutes for the other.

**`margin: 0 auto` is gone.** Content is left-aligned under the sidebar, which is what SaaS layouts do — a centered `max-width` next to a sidebar opens a dead gutter. This also permanently kills the triple duplication of `max-width: 1600px; margin: 0 auto; padding: 0 2rem` that existed in `App.vue` twice and `FilterBar.vue` once. `--content-max` now appears exactly once.

**The sidebar is `sticky`, not `fixed`.** `fixed` forces you to restate the width as a `margin-left` on the content, which is the duplicated-constant problem this redesign removes. Sticky in a grid column keeps one source of truth.

**There is no top bar.** Everything from the old header moves into the sidebar. The `FilterBar` already provides the horizontal band at the top of the content column, so a second chrome region would be redundant.

### Template

```html
<div class="app">
  <aside class="sidebar">
    <div class="sidebar-brand">
      <h1 class="sidebar-brand-name">{{ t('nav.companyName') }}</h1>
      <p class="sidebar-brand-sub" :title="t('nav.subtitle')">{{ t('nav.subtitle') }}</p>
    </div>

    <nav class="sidebar-nav" :aria-label="t('nav.subtitle')">
      <router-link
        class="nav-item"
        to="/"
        :class="{ active: $route.path === '/' }"
        :aria-current="$route.path === '/' ? 'page' : undefined"
      >
        <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
             stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
          <rect x="2.5" y="2.5" width="6" height="6" rx="1.5" />
          <rect x="11.5" y="2.5" width="6" height="6" rx="1.5" />
          <rect x="2.5" y="11.5" width="6" height="6" rx="1.5" />
          <rect x="11.5" y="11.5" width="6" height="6" rx="1.5" />
        </svg>
        <span class="nav-item-label">{{ t('nav.overview') }}</span>
      </router-link>
      <!-- /inventory, /orders, /spending, /demand, /reports follow the same shape -->
    </nav>

    <div class="sidebar-footer">
      <LanguageSwitcher />
      <ProfileMenu
        @show-profile-details="showProfileDetails = true"
        @show-tasks="showTasks = true"
      />
    </div>
  </aside>

  <div class="app-main">
    <FilterBar />
    <main class="main-content">
      <router-view />
    </main>
  </div>

  <!-- modals stay as direct children of .app -->
</div>
```

**Six flat items, existing order preserved** — Overview, Inventory, Orders, Finance, Demand Forecast, Reports. Do not introduce section group headings; at six items grouping is overhead, and each group label would need new locale keys in both files.

Keep the hand-rolled `:class="{ active: $route.path === '/' }"` rather than switching to `router-link-exact-active`, so active-state behavior on `/` stays bit-identical. `aria-current` is new and free.

### Sidebar styles

**Width is 260px, derived not chosen.** `Demand Forecast` at `--text-sm`/500 is about 115px; plus a 20px icon, a 12px gap, 2×16px sidebar padding and 2×12px item padding gives roughly 215px minimum. 240px leaves no headroom; 280px costs content width right at the 1280px cliff where `Spending.vue`'s `minmax(450px, 1fr)` needs 948px. At 260px a 1280px viewport leaves 1020px.

Japanese labels are 2-4 characters (`概要`, `在庫`, `需要予測`) and English runs to 15. **Size for English, then verify in Japanese.**

```css
.sidebar {
  grid-area: sidebar;
  position: sticky;
  top: 0;
  align-self: start;
  height: 100vh;
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-5) var(--space-4);
  background: var(--color-sidebar-bg);
  border-right: 1px solid var(--color-sidebar-border);
  z-index: var(--z-app-chrome);
}

.sidebar-brand {
  padding: var(--space-1) var(--space-3);
  min-width: 0;
}

.sidebar-brand-name {
  font-size: var(--text-sm);
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.01em;
  line-height: 1.25;
}

/* The old .subtitle used border-left as an inline divider. That is meaningless
   once the brand is stacked, so it is not carried over. */
.sidebar-brand-sub {
  font-size: var(--text-xs);
  color: var(--color-text-muted);
  line-height: 1.35;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-nav {
  flex: 1;
  min-height: 0;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.nav-item {
  position: relative;
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-2) var(--space-3);
  border-radius: var(--radius-md);
  color: var(--color-sidebar-item);
  text-decoration: none;
  font-size: var(--text-sm);
  font-weight: 500;
  line-height: 1.25;
  transition: background var(--transition-fast), color var(--transition-fast);
}

.nav-item svg { width: 20px; height: 20px; flex-shrink: 0; }

.nav-item-label {
  min-width: 0;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.nav-item:hover {
  background: var(--color-sidebar-item-hover-bg);
  color: var(--color-text);
}

.nav-item.active {
  background: var(--color-sidebar-item-active-bg);
  color: var(--color-sidebar-item-active);
  font-weight: 600;
}

/* Vertical replacement for the deleted .nav-tabs a.active::after bottom bar.
   left: -16px lands flush on the sidebar's inner edge (padding is --space-4). */
.nav-item.active::before {
  content: '';
  position: absolute;
  left: calc(var(--space-4) * -1);
  top: 50%;
  transform: translateY(-50%);
  width: 3px;
  height: 20px;
  border-radius: 0 2px 2px 0;
  background: var(--color-sidebar-item-active);
}

.nav-item:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.sidebar-footer {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  padding-top: var(--space-4);
  border-top: 1px solid var(--color-border);
}

/* Root elements only — see "Never style a child component's internals" */
.sidebar-footer > * { width: 100%; }
```

A `#ffffff` sidebar on a `#f8fafc` page is a one-step value difference, so the `border-right` is what makes it read as a distinct region. **Use the border and no shadow** — border plus shadow reads dated. If a stronger sidebar is wanted later, a `#0f172a` sidebar is the other conventional option and is a localized change to the `--color-sidebar-*` group only.

### Dropdowns must flip both axes

`ProfileMenu` and `LanguageSwitcher` use `position: absolute; right: 0` opening downward. In a sidebar footer **both axes are wrong** — the menu opens off the left edge and downward past the viewport bottom.

```css
/* LanguageSwitcher.vue and ProfileMenu.vue — in their OWN scoped blocks */
.dropdown-menu {
  position: absolute;
  top: auto;
  bottom: calc(100% + var(--space-2));
  left: 0;
  right: auto;
  min-width: 100%;
  z-index: var(--z-dropdown);
  /* ProfileMenu also needs min-width: 240px to fit the email line */
}
```

`left: 0` is deliberate. `ProfileMenu`'s 240px minimum overhangs the inner sidebar width, and that overhang must go **into the content area**, not off the left edge of the screen.

### Icons

No icon library, no icon font, **no emoji** — this is a business UI. Every icon is inline SVG:

```html
<svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
     stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
```

Size via CSS, never via `width`/`height` attributes. `stroke="currentColor"` inherits the link's hover and active color for free.

Note the deliberate deviation: existing icons use `stroke-width="2"`, which reads heavy at 20px next to 14px text. Sidebar icons use `1.5`, matching the dropdown icons that are already `1.5`.

```html
<!-- Inventory -->
<path d="M2.5 6.5 10 2.5l7.5 4v7L10 17.5l-7.5-4v-7Z" />
<path d="M2.5 6.5 10 10.5l7.5-4M10 10.5v7" />

<!-- Orders -->
<path d="M7 3.5H5.5A1.5 1.5 0 0 0 4 5v11a1.5 1.5 0 0 0 1.5 1.5h9A1.5 1.5 0 0 0 16 16V5a1.5 1.5 0 0 0-1.5-1.5H13" />
<rect x="7" y="2" width="6" height="3" rx="1" />
<path d="M7 9h6M7 12.5h4" />

<!-- Finance -->
<circle cx="10" cy="10" r="7.5" />
<path d="M12.5 7.5H9a1.75 1.75 0 0 0 0 3.5h2a1.75 1.75 0 0 1 0 3.5H7.5M10 5.5v1.5M10 13v1.5" />

<!-- Demand Forecast -->
<path d="M2.5 13.5 7 9l3 3 4.5-5.5" />
<path d="M11.5 6.5h3.5V10" />
<path d="M2.5 17.5h15" stroke-dasharray="2 2" />

<!-- Reports -->
<path d="M11.5 2.5H6A1.5 1.5 0 0 0 4.5 4v12A1.5 1.5 0 0 0 6 17.5h8a1.5 1.5 0 0 0 1.5-1.5V6.5l-4-4Z" />
<path d="M11.5 2.5v4h4M7.5 11h5M7.5 14h3" />
```

Overview is the four-rect grid in the template above.

## Layering And Z-Index

```
--z-raised:      10    in-table dropdowns, tooltips
--z-sticky:      90    FilterBar sticky row
--z-app-chrome: 200    the sidebar
--z-dropdown:  1000    ProfileMenu, LanguageSwitcher
--z-modal:     2000    all modal overlays
```

**The sidebar is 200, not 100.** Using a new number means any un-migrated `z-index: 100` rule visibly loses and surfaces itself instead of silently tying. 200 is above the sticky FilterBar so the bar scrolls under the sidebar's edge, and below 1000 so the footer dropdowns still escape.

**Never write a raw z-index.** If something needs a new layer, add a token. `TasksModal.vue` is the cautionary example: it hardcoded 1000 while the other five overlays used 2000, so it renders below the nav dropdowns.

A `position` plus `z-index` on an ancestor creates a stacking context — a child's `z-index: 2000` cannot escape it. Keep modals as direct children of `.app`, never inside `.sidebar`.

## Shared Primitives

The primitives in `App.vue` are canonical. **Reuse them; never fork them.**

```css
.card {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--space-5);
}
```

A scoped block that redeclares `.card` compiles to `.card[data-v-xxx]` and **out-specifies the global rule**, so the page diverges with no error and no warning.

```css
/* WRONG — redefines the primitive, diverges silently */
.card { border-radius: 12px; box-shadow: var(--shadow-md); }

/* RIGHT — a modifier, promoted to the global block */
.card.elevated      { box-shadow: var(--shadow-sm); border: 0; }
.card.accent-revenue{ border-left: 4px solid var(--color-success); }
```

`Reports.vue` is the worked example of getting this wrong: it redeclares `.card`, `.card-header`, `.card-title`, `.stats-grid`, `.stat-card`, `.stat-label`, `.stat-value`, `.badge`, `.loading` and `.error`, so `/reports` currently renders as a different product from the other five views — 12px radius instead of 10px, shadow instead of border, pill badges instead of 6px uppercase ones. Reconcile it to the globals. Keep only genuinely page-specific classes such as `.reports-table`, `.bar`, `.positive-change`, `.negative-change`.

Modifier naming here is a **separate adjective class**, not a `--` suffix: `.stat-card.warning`, `.badge.success`, `.trend-card.increasing-card`. There is no BEM `__`/`--` anywhere and there are no utility classes. Match that.

**Wide tables belong in `.table-container`.** Its `overflow-x: auto` is why the data tables scroll instead of breaking the shell. Wrap every new table.

## Modal Primitives

Five of the six modal overlays are byte-identical. That is the canonical version:

```css
.modal-overlay {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(15, 23, 42, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: var(--z-modal);
  padding: var(--space-4);
}

.modal-container {
  background: var(--color-surface);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-xl);
  max-width: 700px;
  width: 100%;
  max-height: 90vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}
```

`TasksModal.vue` diverges on all of it — `width`/`height: 100%` instead of the inset properties, `z-index: 1000`, no padding, no `overflow: hidden`. Align it. Vary only `max-width`.

## Responsive Rules

**Prefer an intrinsic fix to a breakpoint.** `minmax(0, 1fr)`, `flex-wrap: wrap`, `min-width: 0`, `max-width` with `height: auto`, and `repeat(auto-fit, minmax(…))` all adapt with no query. Add a breakpoint only when the layout must change *kind*, not size.

Four grids already need nothing — `.stats-grid`, `.kpi-grid`, `Reports.vue`'s `.stats-grid`, and `Spending.vue`'s `.stats-grid-finance` all use `auto-fit minmax()`. That is the pattern to copy.

**This app had zero `@media` queries before this redesign**, so these three constants *are* the convention. Use only these.

```
1280px  content squeeze — multi-column chart grids collapse to one column
1024px  sidebar collapses to icons only (not yet implemented)
 768px  sidebar becomes an off-canvas drawer (not yet implemented)
```

Breakpoint rules live in whatever file owns the selector, not centrally in `App.vue`.

```css
/* Dashboard.vue, scoped */
@media (max-width: 1280px) {
  .charts-grid           { grid-template-columns: minmax(0, 1fr); }
  .order-health-container{ grid-template-columns: minmax(0, 1fr); }
  .donut-chart           { gap: var(--space-6); }
}
```

```css
@media (prefers-reduced-motion: reduce) {
  * { transition-duration: 0.01ms !important; animation-duration: 0.01ms !important; }
}
```

**These are desktop-first `max-width` queries, which knowingly contradicts `client/CLAUDE.md`'s "mobile-first approach".** That guidance predates any media query existing here; with 322 lines of unscoped desktop CSS and roughly 1,500 lines of scoped desktop CSS, mobile-first would mean rewriting every existing rule. This is a deliberate exception, not an oversight.

## Collapsible Sidebar

**Specification only — not implemented.** Implement it here when asked for an icons-only or collapsible sidebar. The shape is fixed now so the change stays mechanical.

- Widths come from the existing tokens. `--sidebar-width-collapsed: 72px` is **deliberately unused until then — do not delete it as dead code.**
- Mechanism is one rule. Redefining the token on `.app` is the whole point; the grid needs no change:
  ```css
  .app.sidebar-collapsed { --sidebar-width: var(--sidebar-width-collapsed); }
  ```
- State: a `ref` in `App.vue`'s `setup()` bound as `:class="{ 'sidebar-collapsed': sidebarCollapsed }"`, persisted to `localStorage` under `app-sidebar-collapsed` — mirroring how `useI18n.js` persists `app-locale`. **Wrap reads and writes in `try`/`catch`**; storage throws in private windows.
- Collapsed rules: `.nav-item-label` and `.sidebar-brand-sub` get `display: none`; `.nav-item` gets `justify-content: center`. Keep the active `::before` bar — it is the only active affordance left.
- **Keep an accessible name.** Labels become `:title` and `:aria-label` on each `.nav-item`; never leave a link with an icon and no name.
- Two new locale keys in **both** `en.js` and `ja.js`: `nav.collapseSidebar`, `nav.expandSidebar`. A mobile drawer trigger also needs `nav.toggleMenu`. Bind `aria-expanded` to the state.
- The footer dropdowns keep `left: 0` and will overhang a 72px rail. That is correct, and is why `left: 0` was chosen over `right: 0`.
- **Do not add `transition: grid-template-columns`.** Animating grid tracks reflows every SVG chart on every frame.

## Adding A Navigation Item

1. Register the route in `client/src/main.js`.
2. Add the label key to **both** `client/src/locales/en.js` and `ja.js`. Missing keys do not throw — `t()` returns the raw key string, so the sidebar renders a literal `nav.something`.
3. Draw the icon as inline SVG per the conventions above.
4. Add the `router-link` with `:class="{ active: ... }"` and `:aria-current`.
5. Re-check the sidebar width against the longest English label.

The existing key-to-route mappings do not match by name. **Preserve them:**

```
nav.overview       ->  /
nav.finance        ->  /spending
nav.demandForecast ->  /demand
```

## Migrating From The Top Nav

Ordered. Steps 2, 3, 5 and 8 fix couplings that break **silently**.

*Delete this section once the migration is verified.*

1. **Add the `:root` block** at the top of `App.vue`'s `<style>`, before the `*` reset. Verify in the browser before touching anything else.
2. **Add `nav.reports`** to `client/src/locales/en.js` and `ja.js` ('Reports' / 'レポート'). Do this **before** the template rewrite — `t()` silently returns the raw key, so a miss renders as literal `nav.reports`, which is easy to overlook.
3. **Replace the hardcoded `Reports` literal** in the nav with `{{ t('nav.reports') }}`. It is the only nav item with no locale key.
4. **Rewrite the template** per the shell section. Preserve the three key-to-route mismatches exactly.
5. **Delete the nav-local CSS**: `.top-nav`, `.nav-container`, `.nav-container > .nav-tabs`, `.nav-container > .language-switcher`, `.logo`, `.logo h1`, `.subtitle`, `.nav-tabs`, `.nav-tabs a`, `:hover`, `.active`, `.active::after`. The `.nav-container > .language-switcher` child selector **must** be replaced by `.sidebar-footer > *`, not just deleted.
6. **Replace `.app`** with the grid; add `.app-main`.
7. **Rewrite `.main-content`**: drop `flex: 1`, `margin: 0 auto` and `width: 100%`; keep `max-width: var(--content-max)`; padding to `var(--space-5) var(--space-6)`.
8. **FilterBar** — `top: 70px` → `top: 0` (the 70px was the old nav height), `z-index: 90` → `var(--z-sticky)`. Remove `max-width: 1600px; margin: 0 auto` from `.filters-container`. **Add `flex-wrap: wrap` and `min-width: 0` to `.filters-grid`** — a no-wrap row of four 140px selects plus nowrap labels is the tightest thing in the app and the highest-priority fix.
9. **Flip both footer dropdowns** to open upward and left-aligned, in their own scoped blocks. Add the full-width button treatment there too.
10. **Fix the donuts intrinsically** — `width: 100%; max-width: 200px; height: auto` so the `viewBox` can do its job. No media query needed.
11. **Add the 1280px breakpoint** in `Dashboard.vue`'s scoped block.
12. **Reconcile the `Reports.vue` forks.** This is a visible change to `/reports` — that is the point. Promote any fork you actually want to a global modifier class.
13. **Fix `Reports.vue`'s `.bar-label`** — the `rotate(-45deg)` has no `transform-origin`, so labels drift out of the chart box. Add `transform-origin: top right` or drop the rotation.
14. **Normalize z-index literals to tokens**, including `TasksModal.vue`'s overlay, and align its overlay and container to the canonical versions.
15. **Remove `'Inter'` from the `body` font stack.** Nothing loads it — there is no font `<link>` in `index.html` — so it silently falls back. Make the stack honest rather than aspirational. Do not add a font link.
16. **Run the semantic hex-to-token pass** over `App.vue` and every file touched above.

## Layout Regression Watchlist

**Any change to sidebar width, content max-width, or breakpoints must be re-verified against these.** Each entry is a rule; the file reference is the canonical implementation of it.

**Any horizontal row of form controls must set `flex-wrap: wrap`.** The content column is 260px narrower than before and fluid below 1600px. Canonical: `.filters-grid` in [FilterBar.vue](client/src/components/FilterBar.vue).

**Never use a bare `1fr` grid track, or a flex child without `min-width: 0`.** A fixed-px descendant will widen the track past the viewport instead of overflowing inside it. Canonical: `.app` and `.app-main` in [App.vue](client/src/App.vue).

**Fixed-size SVG charts need `width: 100%; max-width: Npx; height: auto` plus a `viewBox`.** A hard `width`/`height` pin defeats the viewBox entirely. Canonical: `.donut-svg` and `.donut-svg-compact` in [Dashboard.vue](client/src/views/Dashboard.vue).

**Hard column counts must have a collapse rule.** `repeat(2, 1fr)` with no breakpoint never collapses. Canonical: `.charts-grid` in Dashboard.vue.

**`minmax(450px, 1fr)` needs 948px of content to stay two-up.** At a 1280px viewport minus the 260px sidebar you have 1020px — anything above ~450px per column is at the cliff. See `.two-column-grid` in [Spending.vue](client/src/views/Spending.vue).

**Rotated axis labels must set `transform-origin`.** Unset, they drift out of the chart box as bars narrow. See `.bar-label` in [Reports.vue](client/src/views/Reports.vue).

**Fixed-width labels inside flex rows must be re-checked, never assumed.** `.h-bar-label` (120px, `flex-shrink: 0`) and `.line-bar-label` (12 monthly values, `nowrap`, unrotated) in Dashboard.vue; `.search-box` (300px in a no-wrap `.card-header`) in [Inventory.vue](client/src/views/Inventory.vue).

**`table-layout: fixed` with px column widths scales but clips.** The six widths in [Orders.vue](client/src/views/Orders.vue) sum to 900px and scale proportionally, so they will not overflow — but Japanese names and status badges truncate. Its `.items-dropdown` is anchored in a ~200px cell and will overhang; overhanging into content is acceptable, off-screen is not.

## Verification With Playwright

Use `mcp__playwright__*` as root `CLAUDE.md` mandates. **There are no frontend tests** — `client/package.json` has only `dev`, `build` and `preview` — so this is the only verification path. The dev server runs on port 3000.

**Viewports:** `browser_resize` to 1920×1080, 1440×900, 1280×800, 1024×768. The 1280 row matters most — it is where the 260px sidebar puts `Spending.vue` at its cliff.

**Routes:** `/`, `/inventory`, `/orders`, `/spending`, `/demand`, `/reports` at each width. `Backlog.vue` is unrouted and cannot be checked, which is why its global classes must not be renamed.

**The overflow assertion — the single highest-value check.** `docScroll` must equal `viewport`:

```js
() => ({
  docScroll: document.documentElement.scrollWidth,
  viewport: window.innerWidth,
  overflowing: [...document.querySelectorAll('*')]
    // +2, not +1: sub-pixel rounding on flex children trips +1 with no visible clipping
    .filter(el => el.scrollWidth > el.clientWidth + 2)
    // any *-table-container scrolls by design, not just the shared .table-container class
    .filter(el => !el.closest('[class*="table-container"]'))
    .slice(0, 10)
    .map(el => `${el.tagName}.${el.className}`)
})
```

**`docScroll` versus `viewport` is the assertion that matters.** The per-element list is a diagnostic aid and has two known sources of noise, both handled above:

- **`Spending.vue`'s `.transactions-table-container`** scrolls by design exactly like `.table-container` but does not carry that literal class, so a `.closest('.table-container')` exclusion misses it and it reports as a false positive on every `/spending` check. The `[class*="table-container"]` attribute selector catches both.
- **Sub-pixel rounding** on flex children such as `.kpi-progress-bar` and `.bar-wrapper` exceeds a `+1` threshold by a fraction of a pixel with no visible clipping. `+2` filters that out.

**Tokens actually landed:**

```js
() => ['--color-primary', '--sidebar-width', '--z-app-chrome']
  .map(k => [k, getComputedStyle(document.documentElement).getPropertyValue(k).trim()])
```

**Missing locale keys.** `t()` returns the raw key on a miss, so scan the rendered sidebar. Must be `null`, in both locales:

```js
() => document.querySelector('.sidebar').innerText.match(/nav\.\w+/g)
```

**Dropdown containment.** Click the language button, then the profile button, and for each assert the popover is on-screen and opens upward:

```js
() => { const r = document.querySelector('.dropdown-menu').getBoundingClientRect();
        return { left: r.left, top: r.top, bottom: r.bottom, ok: r.left >= 0 && r.top >= 0 }; }
```

**Sticky behavior.** On `/orders`, scroll to y=600 and assert `.filters-bar` is at y≈0 while `.sidebar` still has `top`≈0.

**Console.** `browser_console_messages` must contain zero errors on every route.

**Screenshots — and actually look at them.** One per route at 1440×900, plus `/reports` before and after the fork removal, since that is the one intentionally changed page. The overflow assertion catches containment but not a squeezed chart or a collided axis label.

## Best Practices

1. **Add the tokens before moving any markup** - The new shell CSS should reference tokens from the start, not hex you replace in a second pass.
2. **Always pair `minmax(0, 1fr)` on the track with `min-width: 0` on the flex child** - Neither substitutes for the other, and together they are the reason existing fixed-px children do not break the shell.
3. **Left-align content, never center it** - No `margin: 0 auto` on `.main-content`. A centered max-width beside a sidebar opens a dead gutter.
4. **Never style a child component's internals from App.vue** - Unscoped selectors tie with the component's scoped rules and the winner flips between dev and production. Target root elements only.
5. **Never rename or delete a shared global class** - `Backlog.vue` has no styles and is unrouted, so breakage there is invisible until someone routes it.
6. **Add locale keys before rewriting the template** - `t()` returns the raw key on a miss, so the failure renders as plausible-looking text instead of throwing.
7. **Split colors by role, not by value** - 600-level for UI chrome and text, 500-level as `--chart-*` for fills. `#3b82f6` fails contrast for text at 3.1:1.
8. **Extend primitives with modifier classes, never redefine them** - A scoped `.card` out-specifies the global `.card` and diverges with no error.
9. **Prefer an intrinsic fix to a breakpoint** - `flex-wrap`, `minmax()`, `max-width` with `height: auto`. Add a query only when the layout changes kind.
10. **Verify with the overflow assertion, then look at a screenshot** - `scrollWidth === clientWidth` catches containment failures; only your eyes catch a collided axis label.

## Key Reminders

- **One global stylesheet** - `App.vue`, no `scoped`. Everything else is `<style scoped>`.
- **`views/Backlog.vue` has no styles and no route** - It runs entirely on global classes and cannot be visually verified.
- **`nav.reports` does not exist** - The Reports nav item is a hardcoded English literal. Add the key to both locale files.
- **Locale keys do not match route names** - `nav.finance` → `/spending`, `nav.overview` → `/`, `nav.demandForecast` → `/demand`.
- **`'Inter'` is never loaded** - There is no font link in `index.html`. Make the stack honest; do not add one.
- **`var()` does not work in `@media`** - Breakpoints are documented constants: 1280 / 1024 / 768.
- **`viewBox` cannot scale an SVG that CSS pins** - Use `max-width` plus `width: 100%`.
- **No raw z-index values** - Use the five tokens. The sidebar is 200 on purpose, so stale `100` rules surface.
- **No emojis, no icon library, no icon font** - Inline SVG with `stroke="currentColor"`.
