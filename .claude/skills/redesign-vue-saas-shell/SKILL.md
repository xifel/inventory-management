---
name: redesign-vue-saas-shell
description: Redesign a Vue 3 application's UI shell into a modern SaaS interface with a collapsible left sidebar (icon + label, ~240px expanded / ~64px collapsed), bottom-anchored profile and locale switcher, and a CSS-custom-property design-token system. Use when the user asks to modernize, redesign, or polish a Vue 3 app's layout, replace a top nav with a sidebar, apply a Linear/Notion/Vercel/Stripe SaaS aesthetic, introduce design tokens, or restructure navigation chrome. Keeps existing routes, i18n keys, and component logic intact.
---

# Redesign Vue 3 SaaS Shell

A repeatable recipe for turning a typical Vue 3 app — top nav, hardcoded styles, mixed spacing — into a modern SaaS shell with a collapsible left sidebar, a CSS-variable design-token system, and consistent layout rhythm. Mid-prescriptive: the steps are concrete, the look is opinionated within reason, but the skill leaves room for the target app's existing aesthetic and component library.

## 1. When to use this skill

Trigger this skill when the user asks to:

- "redesign", "modernize", or "polish" a Vue 3 app's UI shell
- replace a top navigation bar with a left sidebar (or vice versa, in reverse)
- apply a "SaaS look", "Linear-style", "Notion-style", "Vercel-style", or "Stripe-style" UI
- introduce a design-token / CSS custom property system
- fix inconsistent spacing, padding, or chrome weight
- restructure navigation chrome generally

**Hand off to other skills when:**

- The user wants greenfield component invention, marketing-page aesthetics, motion-as-delight, or distinctive visual identity → use `frontend-design`. This skill stays in the lane of *layout, sidebar mechanics, tokens, and migration*. It deliberately does NOT prescribe a palette beyond a neutral skeleton.
- The user wants to brainstorm whether a redesign is the right call → use `brainstorming` first.
- The work needs a written plan and review checkpoints → use `writing-plans` after this skill's audit.

## 2. Design principles

1. **Tokens first, values never.** Every spacing, color, radius, and font-size value goes through a CSS custom property. Hardcoded `2rem` or `#0f172a` in component styles is a smell.
2. **Sidebar as spine.** The sidebar is the persistent identity of the app. Logo at the top, primary nav next, secondary/utility nav below, profile + locale + collapse toggle anchored at the bottom. Nothing else.
3. **Content takes the rest.** The main content area is a single grid column that expands and contracts as the sidebar collapses. No nested fixed widths fighting the sidebar.
4. **Reduce chrome weight.** Borders, shadows, and dividers should be barely visible. The hierarchy comes from spacing and type weight, not boxes within boxes.
5. **Motion is structural, not decorative.** Use motion to communicate layout changes (sidebar collapse, route transitions). Honor `prefers-reduced-motion`.
6. **Accessibility is non-negotiable.** WCAG AA contrast, visible focus rings, keyboard navigation through sidebar items, ARIA roles on the nav.

## 3. Target anatomy

```
┌──────────────┬─────────────────────────────────────────────┐
│  [Logo]      │  [Page header — title, breadcrumbs, actions]│
│              │                                             │
│  Primary     │                                             │
│  ─ Overview  │                                             │
│  ─ Inventory │                                             │
│  ─ Orders    │                                             │
│  ─ ...       │                Main content                 │
│              │                                             │
│  Secondary   │                                             │
│  ─ Reports   │                                             │
│              │                                             │
│  ──────────  │                                             │
│  [Profile]   │                                             │
│  [Locale]    │                                             │
│  [Collapse]  │                                             │
└──────────────┴─────────────────────────────────────────────┘
   --sidebar-w               1fr
```

Sidebar widths come from tokens: `--sidebar-w-expanded` (~240-260px) and `--sidebar-w-collapsed` (~64px). The `--sidebar-w` variable swaps between them; the layout grid never recalculates from JS.

## 4. Pre-flight audit checklist

Before touching any code, inventory the target app:

- [ ] Locate the app shell file (usually `App.vue`). Note: top nav structure, embedded styles, fixed header height, header background, and any other layout chrome.
- [ ] Locate the router config (usually `main.js` or `router/index.js`). The route list IS your sidebar items.
- [ ] Find how nav labels are translated. If there's an i18n composable or `vue-i18n` instance, capture the existing key namespace (often `nav.*`). **The redesign must preserve every existing key.**
- [ ] Grep for `position: sticky` and `top: <px>` across the codebase. Any value tied to the old header height (e.g. `top: 70px`) is a downstream offset that needs updating.
- [ ] Grep for hardcoded hex colors and rem/px values in `<style>` blocks. List the most common ones — these become token candidates.
- [ ] Identify utility components that currently sit in the header (profile menu, locale switcher, search, notifications). They will move to the sidebar footer or the new page-header.
- [ ] Note the icon strategy: is there an icon library installed (lucide, heroicons, FontAwesome)? Are SVGs inlined? **Don't add a dependency without asking.**

> **In this repo (inventory-management):**
> - Shell + global styles: `client/src/App.vue` (lines ~1-160 template, ~164-486 styles — the bulk of the rewrite happens here)
> - Routes: `client/src/main.js` — 6 routes that map 1:1 to sidebar items
> - i18n: `client/src/composables/useI18n.js` (custom, not vue-i18n); preserve `t('nav.*')` keys from `client/src/locales/en.js` and `ja.js`
> - Sticky offset to fix: `client/src/components/FilterBar.vue` uses `top: 70px` — must move to `0` or `var(--page-header-h)` after the top nav is gone
> - Utilities to relocate: `client/src/components/ProfileMenu.vue` and `client/src/components/LanguageSwitcher.vue`
> - Untouched: every `*DetailModal.vue`, `TasksModal.vue` — modals are independent of shell chrome

## 5. Step 1 — Install design tokens

Inject a `:root` block at the top of the global stylesheet (in this app, the `<style>` in `App.vue`; in others, it might be `assets/css/tokens.css` imported from `main.js`).

Minimum viable token set — start here, expand as needed:

```css
:root {
  /* Color — neutral surface */
  --color-bg-app:       #f8fafc;
  --color-bg-surface:   #ffffff;
  --color-bg-sidebar:   #0f172a;
  --color-fg-primary:   #0f172a;
  --color-fg-secondary: #64748b;
  --color-fg-on-dark:   #e2e8f0;
  --color-border:       #e2e8f0;
  --color-accent:       #2563eb;
  --color-accent-soft:  #dbeafe;

  /* Spacing — 4px base */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-5: 1.5rem;
  --space-6: 2rem;
  --space-7: 3rem;

  /* Sidebar geometry */
  --sidebar-w-expanded:  260px;
  --sidebar-w-collapsed: 64px;
  --sidebar-w:           var(--sidebar-w-expanded);
  --page-header-h:       56px;

  /* Radius + shadow */
  --radius-sm: 6px;
  --radius-md: 10px;
  --shadow-pop: 0 4px 16px rgba(15, 23, 42, 0.08);

  /* Motion */
  --motion-fast: 120ms;
  --motion-base: 200ms;
  --ease-out:    cubic-bezier(0.2, 0.8, 0.2, 1);
}

@media (prefers-reduced-motion: reduce) {
  :root {
    --motion-fast: 0ms;
    --motion-base: 0ms;
  }
}
```

For the full token table (typography, status colors, semantic spacing roles), see `references/design-tokens.md`.

After tokens are in place, sweep the existing styles and replace hardcoded values: `padding: 1.25rem` → `padding: var(--space-5)`, `color: #0f172a` → `color: var(--color-fg-primary)`. Do this incrementally — don't try to convert every value in one pass.

## 6. Step 2 — Build the sidebar shell

Replace the top-nav block in `App.vue` with a two-column layout grid.

```html
<template>
  <div class="app-shell" :class="{ 'is-collapsed': isCollapsed }">
    <Sidebar
      :collapsed="isCollapsed"
      @toggle="isCollapsed = !isCollapsed"
    />
    <main class="app-main">
      <FilterBar />
      <router-view />
    </main>
  </div>
</template>

<style scoped>
.app-shell {
  display: grid;
  grid-template-columns: var(--sidebar-w) 1fr;
  min-height: 100vh;
  transition: grid-template-columns var(--motion-base) var(--ease-out);
}
.app-shell.is-collapsed { --sidebar-w: var(--sidebar-w-collapsed); }
.app-main {
  background: var(--color-bg-app);
  min-width: 0; /* prevents grid blowout */
}
</style>
```

The collapse mechanism is a single CSS variable swap on the parent — no JavaScript layout calculation. Width changes ripple naturally because every consumer references `--sidebar-w`.

For the full `Sidebar.vue` SFC (with router-link list, active state, ARIA roles, keyboard nav, mobile drawer behavior, footer slot), see `references/sidebar-shell-template.md`.

**Icon strategy:** if the app already uses `lucide-vue-next`, `@heroicons/vue`, or similar — use it. If not, prefer inline SVG over a new dependency. Each nav item gets a small (16-20px) icon at the leading edge. Labels disappear (or fade out via `opacity` + `width: 0`) when collapsed; icons stay.

## 7. Step 3 — Relocate profile and locale

The components in the old header (typically a profile menu and a locale switcher) move into the sidebar footer:

- They become children of the `Sidebar.vue` footer slot.
- Popovers/dropdowns must open **upward and rightward**, away from the sidebar's right edge — set the popover's `bottom: 100%` and align to the trigger, then offset to escape the sidebar bounds.
- When the sidebar is collapsed, only the avatar / globe icon shows; the popover still works on click.

> **In this repo:** mount `<ProfileMenu />` and `<LanguageSwitcher />` inside `Sidebar.vue`'s footer. Their existing internals (composables, props, modal triggers) don't change. The popover positioning CSS does — verify they don't get clipped by `Sidebar.vue`'s `overflow: hidden` (use `overflow: visible` on the footer or render popovers via a teleport/portal).

## 8. Step 4 — Fix dependent sticky offsets

The old top nav was likely `position: sticky; top: 0` with a fixed height. Anything else in the app sticky-positioned below it (filter bars, sub-headers, tab strips) carries a magic-number offset like `top: 70px`. After removing the top nav:

- Sub-header sticky elements should now be `top: 0` (or `top: var(--page-header-h)` if a new in-content page header was introduced).
- Run `grep -rn "top: " client/src/` (or equivalent) and audit each hit.

> **In this repo:** `client/src/components/FilterBar.vue` has `top: 70px`. Change to `top: 0`.

## 9. Step 5 — Collapse behavior and persistence

```js
// Inside App.vue or a useSidebar composable
import { ref, watch, onMounted } from 'vue'

const isCollapsed = ref(false)

onMounted(() => {
  const saved = localStorage.getItem('sidebar:collapsed')
  if (saved !== null) isCollapsed.value = saved === 'true'
})

watch(isCollapsed, (v) => {
  localStorage.setItem('sidebar:collapsed', String(v))
})
```

Mobile (under ~768px): the sidebar should NOT take a fixed grid column. It becomes an overlay drawer that slides in over content. A simple media query swap:

```css
@media (max-width: 768px) {
  .app-shell {
    grid-template-columns: 0 1fr;
  }
  .app-shell .sidebar {
    position: fixed;
    inset: 0 auto 0 0;
    width: var(--sidebar-w-expanded);
    transform: translateX(-100%);
    transition: transform var(--motion-base) var(--ease-out);
    z-index: 50;
  }
  .app-shell.is-mobile-open .sidebar { transform: translateX(0); }
}
```

A backdrop/scrim and an Escape-key handler close the mobile drawer.

## 10. Verification checklist

After the redesign, confirm every item:

- [ ] `npm run dev` starts cleanly with no console errors or warnings.
- [ ] All routes from `main.js` are reachable via sidebar links.
- [ ] Active route's sidebar item is visually highlighted.
- [ ] Every `t('nav.*')` key still resolves; switch locale and confirm labels update.
- [ ] Sidebar collapse persists across page reload.
- [ ] `prefers-reduced-motion: reduce` disables collapse and route transitions.
- [ ] Below 768px viewport, sidebar becomes an overlay drawer; backdrop + Escape close it.
- [ ] `grep -rn "top: 70px" client/src/` (or the old header value) returns zero hits.
- [ ] `grep -rnE "color:\\s*#[0-9a-fA-F]{3,6}" client/src/components/` is dramatically smaller than before — most colors go through tokens now.
- [ ] Profile menu and locale switcher popovers render fully on-screen, not clipped by sidebar.
- [ ] Visual: WCAG AA contrast on sidebar text vs sidebar background; focus rings visible on keyboard tab.
- [ ] No new TypeScript or lint errors.
- [ ] Smoke test: open every view in the browser, click around, verify modals still open and close, filters still apply.

For UI changes, run the dev server and verify in a browser per the project's CLAUDE.md — type checks alone do not validate the redesign.

## 11. Adapting to other Vue 3 apps

This skill assumes a fairly minimal Vue 3 stack. Adjustments by environment:

- **Pinia for state:** put `isCollapsed` in a `useUiStore` Pinia store instead of a local ref.
- **vue-i18n proper:** the `t()` calls are identical; only the import changes (`useI18n` from `vue-i18n` vs the custom composable).
- **Tailwind / UnoCSS:** tokens go in the Tailwind config (`theme.extend.colors`, `theme.extend.spacing`) instead of `:root`. The grid, sidebar widths, and layout patterns are otherwise the same — express them as utility classes.
- **Component library (Vuetify, Element Plus, PrimeVue, Naive UI):** most ship a navigation drawer or app-shell component. Use it as the structural skeleton, but apply the tokens, spacing scale, and footer-anchored profile/locale pattern from this skill.
- **TypeScript:** the snippets above translate directly; type `isCollapsed` as `Ref<boolean>`.
- **SSR (Nuxt):** read `localStorage` only inside `onMounted` (already shown above) to avoid hydration mismatches.

What stays constant across all environments:

- Token-driven values, never hardcoded.
- Two-column grid with `var(--sidebar-w) 1fr`.
- Sidebar = logo / primary nav / secondary nav / footer (profile, locale, collapse).
- Collapse via CSS variable swap, not JS layout recalc.
- Mobile = overlay drawer.
- i18n keys preserved — the redesign is layout, not content.

## Reference files

- `references/sidebar-shell-template.md` — full `Sidebar.vue` SFC with template, script, styles, ARIA, mobile behavior. Plus the `App.vue` outer layout.
- `references/design-tokens.md` — complete token tables (color, spacing, type, radius, shadow, motion) and a minimum-viable subset.
- `references/migration-checklist.md` — ordered diff-style steps for converting an existing top-nav app to the sidebar shell.
- `references/pitfalls.md` — common mistakes and gotchas (FOUC, z-index collisions, popover clipping, sticky-offset cascades).
