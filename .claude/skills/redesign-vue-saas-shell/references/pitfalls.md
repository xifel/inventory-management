# Pitfalls & Gotchas

Things that go wrong during a top-nav-to-sidebar redesign. Most of these have bitten real projects. When you hit one, the fix is usually one of the patterns below.

---

## 1. FOUC on collapse-state hydration

**Symptom:** the page loads with the sidebar expanded, then "snaps" to collapsed a beat later (because `localStorage` is read in `onMounted`).

**Why:** `localStorage` is only available after mount, so the initial render uses the default (`isCollapsed = false`).

**Fix options:**

- Set the initial CSS variable at the document level *before* Vue mounts, by reading `localStorage` in a small script in `index.html`:
  ```html
  <script>
    try {
      const c = localStorage.getItem('sidebar:collapsed') === 'true';
      if (c) document.documentElement.style.setProperty('--sidebar-w', '64px');
    } catch (_) {}
  </script>
  ```
- Or accept the flash and disable the transition for the first 100ms after mount.
- For SSR (Nuxt): persist via cookie, read it server-side, hydrate with the correct state from the start.

---

## 2. Z-index collisions between sidebar and modals

**Symptom:** opening `InventoryDetailModal` (or any existing modal) renders the modal *below* the sidebar, or the sidebar's collapse toggle pokes through the modal backdrop.

**Why:** the existing modals likely use `z-index: 1000` (a magic number) while the sidebar template uses `z-index: 10`. Without coordination, stacking is unpredictable as more layers get added.

**Fix:** unify behind the `--z-*` token scale (see `design-tokens.md`). Modals get `var(--z-modal)`, sidebar gets `var(--z-sidebar)`, popovers `var(--z-popover)`. Audit every existing component for raw `z-index` values and replace them.

---

## 3. Locale switcher / profile menu popover gets clipped

**Symptom:** the popover opens but only the part inside the sidebar shows; the rest is cut off.

**Why:** `Sidebar.vue` uses `overflow: hidden` so labels can fade away cleanly when collapsed. That same `overflow: hidden` clips popovers.

**Fix:**

- Render the popover via `<Teleport to="body">` from inside the component. The popover then escapes the sidebar's stacking and overflow context.
- Position it absolutely with `bottom: <trigger-y>` and `left: var(--sidebar-w)` so it appears just to the right of the sidebar.
- Add a small offset (~8px) so it doesn't hug the sidebar edge.

---

## 4. Sticky offset cascade

**Symptom:** after removing the top nav, the FilterBar (or any other sticky element) appears halfway down the page, or with a weird gap above it, or doesn't stick at all.

**Why:** the sticky element had `top: 70px` (the old nav height). Without the top nav above it, that 70px is now 70px of dead space at the top of the viewport before the bar starts sticking.

**Fix:** set `top: 0` (if there's no in-content header) or `top: var(--page-header-h)` (if you introduced one). `grep -rn "top:" client/src/` to find every offender.

---

## 5. Grid blowout from minimum content widths

**Symptom:** wide tables or charts in the main content force the layout to scroll horizontally, breaking the sidebar/content alignment.

**Why:** CSS Grid auto-sized columns respect the intrinsic minimum width of their content. A table with many columns can push the `1fr` content column wider than the viewport, which then pushes the sidebar off-screen.

**Fix:** add `min-width: 0` to `.app-main`. This tells the grid that the content column is allowed to shrink below its content's intrinsic width, and the table will get its own internal scroll bar instead.

```css
.app-main {
  min-width: 0;
}
```

---

## 6. Active route highlighting on `/` parent

**Symptom:** the "Overview" / Dashboard nav item stays highlighted even when you're on `/inventory` or `/orders`.

**Why:** vue-router's default `active-class` matches by path prefix. `'/'` is a prefix of every path.

**Fix:** use `exact-active-class` for the home route, or for all primary nav items if you want strict matching:

```html
<router-link :to="item.to" exact-active-class="is-active">
```

Or use the `:exact="true"` prop (Vue Router 3) / rely on `exact-active-class` (Vue Router 4).

---

## 7. Iconography drift

**Symptom:** the sidebar has line icons at 18px, but a button somewhere else uses a filled icon at 16px from a different library, and the page header has emoji.

**Why:** without an icon decision up front, every contributor reaches for a different source.

**Fix:** pick one source (lucide-vue-next is a safe default if no library is installed) and one size (18px in nav, 16px inline with text, 20-24px on standalone buttons). Document the choice in the project's CLAUDE.md.

---

## 8. Dark sidebar contrast traps

**Symptom:** the sidebar uses `--color-bg-sidebar: #0f172a` and `--color-fg-on-dark: #e2e8f0`, but secondary nav items use `rgba(255,255,255,0.4)` and they fail WCAG AA at 4.5:1 contrast.

**Fix:** use at least `rgba(255,255,255,0.55)` (~5.0:1) for de-emphasized text on the dark sidebar, or switch to a defined `--color-fg-on-dark-muted` token at `#94a3b8`. Run a contrast checker before shipping.

---

## 9. Over-tokenizing on day one

**Symptom:** you spent two days defining 80 tokens before writing any component code.

**Why:** the temptation to "do it right" once at the start.

**Fix:** start with the minimum viable subset from `design-tokens.md`. Add tokens *as you encounter the third occurrence* of a value. Tokens you defined but never used are noise.

---

## 10. Mobile drawer doesn't trap focus

**Symptom:** keyboard tab on mobile drawer escapes the drawer and reaches content behind the scrim.

**Fix:** when the drawer is open, set `tabindex="-1"` on the main content, or use the `inert` attribute (broadly supported in modern browsers): `<main :inert="mobileOpen">`. Pair with returning focus to the trigger button when the drawer closes.

---

## 11. i18n key drift

**Symptom:** you copied the sidebar template but forgot to add `nav.expand` / `nav.collapse` / `nav.more` to your locale files. Buttons render as raw key paths or empty strings.

**Fix:** the audit step in `SKILL.md` §4 calls this out. After installing the sidebar, search for every `t('nav.…')` invocation in `Sidebar.vue` and verify each key exists in every locale file (`en.js`, `ja.js`, …).

---

## 12. Transitioning `grid-template-columns` is jank-prone in some browsers

**Symptom:** the sidebar collapse animation stutters or doesn't animate at all.

**Why:** historically, transitioning `grid-template-columns` was unsupported. Modern Chrome/Firefox/Safari handle it, but very old browsers may not.

**Fix:** if you support older browsers, transition `width` on the sidebar element directly (and use `position: fixed` with `padding-left: var(--sidebar-w)` on `.app-main`) instead of relying on grid transitions. The CSS variable approach used in the template is broadly compatible, but worth verifying against your supported browser matrix.
