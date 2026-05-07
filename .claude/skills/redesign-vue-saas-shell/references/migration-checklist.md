# Migration Checklist: Top Nav → Sidebar

Concrete diff-style steps for converting an existing Vue 3 app from a top-nav layout to the SaaS sidebar shell. Work through these in order — each step assumes the previous is complete.

---

## 0. Branch and snapshot

- Create a feature branch (`feat/sidebar-redesign` or similar).
- Take a "before" screenshot of every top-level route — useful for visual regression checks at the end.

## 1. Inventory the current shell

- [ ] Open `App.vue`. Identify the top-level template structure (header, nav, main, footer) and the embedded `<style>` block.
- [ ] Count the nav links. They become sidebar items 1:1.
- [ ] Identify where the profile menu, locale switcher, search, notifications, etc. currently live in the header.
- [ ] Record the header height (look for `height: 70px`, `padding: 1rem 2rem`, etc.). This is the magic number that downstream sticky elements depend on.
- [ ] Check `main.js` (or `router/index.js`) for the route list. Map each route's `name` or `path` to a label key.

## 2. Install design tokens

- [ ] Add a `:root` block with the **minimum viable subset** from `design-tokens.md` to the global `<style>` in `App.vue` (or a new `assets/css/tokens.css` imported from `main.js`).
- [ ] Verify in the browser that nothing visually changed (you've added tokens but no consumers yet).
- [ ] Optional: also drop in `--motion-*` and the `prefers-reduced-motion` override now so step 7 has them ready.

## 3. Add the `Sidebar.vue` component

- [ ] Copy the SFC from `references/sidebar-shell-template.md` to `client/src/components/Sidebar.vue`.
- [ ] Adjust the `primary` and `secondary` arrays to match your routes. The `label` field is an i18n key path (e.g. `'nav.overview'`).
- [ ] Replace the placeholder `ProfileMenu` and `LanguageSwitcher` imports with your real component paths.
- [ ] If your i18n composable name or import path differs, fix the `useI18n` import.
- [ ] Add any missing locale keys: at minimum `nav.expand`, `nav.collapse`, and (if used) `nav.more`, `nav.primary`.

## 4. Rewrite `App.vue` outer layout

- [ ] Replace the current top-nav-and-main template with the layout grid from `sidebar-shell-template.md`.
- [ ] Move the `isCollapsed` state, the `onMounted` localStorage read, and the `watch` persistence into `<script setup>`.
- [ ] Delete the old `<header>` block and its associated styles.
- [ ] Ensure `<router-view />` still mounts inside `.app-main`.

## 5. Relocate header utilities

- [ ] `ProfileMenu.vue`: ensure it now lives inside `Sidebar.vue`'s footer slot (default slot population is in the template).
- [ ] `LanguageSwitcher.vue`: same.
- [ ] Verify their dropdown popovers still render fully on-screen. If they're clipped by `Sidebar.vue`'s `overflow: hidden`, wrap the popover in `<Teleport to="body">` inside the component.
- [ ] Remove any `position: absolute; right: 1rem` or similar header-corner positioning from these components — they no longer need it.

## 6. Fix dependent sticky offsets

- [ ] `grep -rn "top: 70px" client/src/` (or your old header height value).
- [ ] For each hit, change the `top` to `0` (no in-content header) or `var(--page-header-h)` (if you introduced one).
- [ ] Common offenders: `FilterBar.vue`, sub-tab navigation, table sticky headers.

## 7. Wire up collapse persistence and mobile

- [ ] Confirm `localStorage.getItem('sidebar:collapsed')` is read on mount and `localStorage.setItem` on change.
- [ ] Test the toggle: click → sidebar collapses → reload → still collapsed. Click → expand → reload → expanded.
- [ ] Resize browser to <768px. Verify the sidebar becomes a hidden drawer; opening it shows a backdrop; clicking the backdrop or pressing Escape closes it.
- [ ] Set OS to "reduce motion". Verify the collapse transition is instant.

## 8. Token-ify spacing and color (incremental)

- [ ] Sweep `client/src/components/*.vue` and replace hardcoded values incrementally. Don't try to do every component in one PR.
- [ ] Priority order: `App.vue` (already done) → `Sidebar.vue` (already done) → `FilterBar.vue` → views (`Dashboard.vue`, `Inventory.vue`, …) → modals.
- [ ] Use the rule: any value used 3+ times across the codebase becomes a token reference.

## 9. Active state and focus

- [ ] Click each sidebar item; confirm the corresponding view loads AND the sidebar item shows active state.
- [ ] Tab through the sidebar with the keyboard. Each item should show a visible focus ring.
- [ ] Tab through `ProfileMenu` and `LanguageSwitcher`. Their popovers should open via Enter/Space, close via Escape.

## 10. Verification pass

Run the verification checklist from `SKILL.md` §10. Address any failures before merging.

## 11. Cleanup

- [ ] Remove dead CSS — anything tied to the old top-nav header.
- [ ] Remove unused i18n keys (e.g. if a "Sign In" link was in the header but now lives in the profile menu).
- [ ] Update screenshots in any docs (`README.md`, `docs/`) that show the old top-nav.
- [ ] Update CLAUDE.md if it references the old layout.

## 12. Commit strategy

A clean commit sequence makes review easier:

1. `feat: add design tokens at :root`
2. `feat: add Sidebar component (not yet wired in)`
3. `feat(shell): replace top nav with sidebar layout`
4. `refactor: relocate ProfileMenu and LanguageSwitcher to sidebar footer`
5. `fix: update sticky offsets after top nav removal`
6. `refactor(tokens): replace hardcoded spacing/colors in <component>` (one per component, optional)

Each commit should leave the app in a runnable state.
