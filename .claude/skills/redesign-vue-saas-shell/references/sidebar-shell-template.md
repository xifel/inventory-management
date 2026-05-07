# Sidebar Shell Template

A copy-pasteable starting point for the collapsible left sidebar and the outer app layout. Adapt names and i18n keys to your app, but keep the structural patterns.

---

## `Sidebar.vue`

```vue
<template>
  <aside
    class="sidebar"
    :class="{ 'is-collapsed': collapsed, 'is-mobile-open': mobileOpen }"
    role="navigation"
    :aria-label="t('nav.primary') || 'Primary navigation'"
  >
    <!-- Brand -->
    <div class="brand">
      <router-link to="/" class="brand-link">
        <span class="brand-mark" aria-hidden="true">
          <!-- Inline SVG logo or <img> here -->
          <svg viewBox="0 0 24 24" width="24" height="24"><circle cx="12" cy="12" r="10" fill="currentColor"/></svg>
        </span>
        <span class="brand-text">{{ t('nav.companyName') }}</span>
      </router-link>
    </div>

    <!-- Primary nav -->
    <nav class="nav-section">
      <ul class="nav-list">
        <li v-for="item in primary" :key="item.to">
          <router-link
            :to="item.to"
            class="nav-link"
            active-class="is-active"
            exact-active-class="is-exact"
          >
            <span class="nav-icon" aria-hidden="true" v-html="item.icon"></span>
            <span class="nav-label">{{ t(item.label) }}</span>
          </router-link>
        </li>
      </ul>
    </nav>

    <!-- Secondary nav (optional) -->
    <nav class="nav-section nav-section--secondary" v-if="secondary.length">
      <p class="nav-heading">{{ t('nav.more') }}</p>
      <ul class="nav-list">
        <li v-for="item in secondary" :key="item.to">
          <router-link :to="item.to" class="nav-link" active-class="is-active">
            <span class="nav-icon" aria-hidden="true" v-html="item.icon"></span>
            <span class="nav-label">{{ t(item.label) }}</span>
          </router-link>
        </li>
      </ul>
    </nav>

    <!-- Footer cluster: profile, locale, collapse toggle -->
    <div class="sidebar-footer">
      <slot name="footer">
        <ProfileMenu />
        <LanguageSwitcher />
      </slot>
      <button
        class="collapse-toggle"
        type="button"
        @click="$emit('toggle')"
        :aria-pressed="collapsed"
        :aria-label="collapsed ? t('nav.expand') : t('nav.collapse')"
      >
        <svg viewBox="0 0 24 24" width="18" height="18" aria-hidden="true">
          <path d="M15 6l-6 6 6 6" fill="none" stroke="currentColor" stroke-width="2"/>
        </svg>
      </button>
    </div>
  </aside>
</template>

<script setup>
import { onMounted, onUnmounted } from 'vue'
import { useI18n } from '@/composables/useI18n' // or 'vue-i18n'
import ProfileMenu from '@/components/ProfileMenu.vue'
import LanguageSwitcher from '@/components/LanguageSwitcher.vue'

const props = defineProps({
  collapsed: { type: Boolean, default: false },
  mobileOpen: { type: Boolean, default: false },
})
const emit = defineEmits(['toggle', 'close-mobile'])

const { t } = useI18n()

// Map your routes to nav items. Icons are inline SVG strings here for portability.
const primary = [
  { to: '/',          label: 'nav.overview',       icon: '<svg viewBox="0 0 24 24" width="18" height="18"><rect x="3" y="3" width="7" height="7" fill="none" stroke="currentColor" stroke-width="2"/><rect x="14" y="3" width="7" height="7" fill="none" stroke="currentColor" stroke-width="2"/><rect x="3" y="14" width="7" height="7" fill="none" stroke="currentColor" stroke-width="2"/><rect x="14" y="14" width="7" height="7" fill="none" stroke="currentColor" stroke-width="2"/></svg>' },
  { to: '/inventory', label: 'nav.inventory',      icon: '<svg viewBox="0 0 24 24" width="18" height="18"><path d="M3 7l9-4 9 4-9 4-9-4z M3 7v10l9 4 9-4V7" fill="none" stroke="currentColor" stroke-width="2"/></svg>' },
  { to: '/orders',    label: 'nav.orders',         icon: '<svg viewBox="0 0 24 24" width="18" height="18"><path d="M4 7h16M4 12h16M4 17h10" fill="none" stroke="currentColor" stroke-width="2"/></svg>' },
  { to: '/spending',  label: 'nav.finance',        icon: '<svg viewBox="0 0 24 24" width="18" height="18"><path d="M12 1v22M17 5H9.5a3.5 3.5 0 000 7h5a3.5 3.5 0 010 7H6" fill="none" stroke="currentColor" stroke-width="2"/></svg>' },
  { to: '/demand',    label: 'nav.demandForecast', icon: '<svg viewBox="0 0 24 24" width="18" height="18"><path d="M3 17l6-6 4 4 8-8" fill="none" stroke="currentColor" stroke-width="2"/></svg>' },
]
const secondary = [
  { to: '/reports', label: 'nav.reports', icon: '<svg viewBox="0 0 24 24" width="18" height="18"><path d="M9 17V7M14 17v-5M19 17v-2M4 21h16" fill="none" stroke="currentColor" stroke-width="2"/></svg>' },
]

// Escape closes mobile drawer
const onKey = (e) => { if (e.key === 'Escape' && props.mobileOpen) emit('close-mobile') }
onMounted(() => window.addEventListener('keydown', onKey))
onUnmounted(() => window.removeEventListener('keydown', onKey))
</script>

<style scoped>
.sidebar {
  display: flex;
  flex-direction: column;
  background: var(--color-bg-sidebar);
  color: var(--color-fg-on-dark);
  padding: var(--space-4) var(--space-3);
  width: var(--sidebar-w);
  transition: width var(--motion-base) var(--ease-out);
  overflow: hidden;
  position: relative;
  z-index: 10;
}

.brand {
  margin-bottom: var(--space-5);
  padding: 0 var(--space-2);
}
.brand-link {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  color: inherit;
  text-decoration: none;
}
.brand-mark { color: var(--color-accent); flex-shrink: 0; }
.brand-text {
  font-weight: 600;
  font-size: 0.95rem;
  white-space: nowrap;
  opacity: 1;
  transition: opacity var(--motion-fast) var(--ease-out);
}

.nav-section { margin-bottom: var(--space-4); }
.nav-heading {
  text-transform: uppercase;
  letter-spacing: 0.06em;
  font-size: 0.7rem;
  color: rgba(255, 255, 255, 0.4);
  margin: 0 var(--space-2) var(--space-2);
}
.nav-list { list-style: none; margin: 0; padding: 0; }

.nav-link {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-2) var(--space-3);
  border-radius: var(--radius-sm);
  color: rgba(255, 255, 255, 0.7);
  text-decoration: none;
  font-size: 0.875rem;
  white-space: nowrap;
  transition: background var(--motion-fast), color var(--motion-fast);
}
.nav-link:hover {
  background: rgba(255, 255, 255, 0.06);
  color: var(--color-fg-on-dark);
}
.nav-link.is-active {
  background: rgba(255, 255, 255, 0.1);
  color: white;
}
.nav-link:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}
.nav-icon {
  display: inline-flex;
  width: 18px;
  height: 18px;
  flex-shrink: 0;
}
.nav-label {
  opacity: 1;
  transition: opacity var(--motion-fast) var(--ease-out);
}

/* Footer */
.sidebar-footer {
  margin-top: auto;
  padding-top: var(--space-4);
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
}
.collapse-toggle {
  align-self: flex-end;
  background: transparent;
  color: rgba(255, 255, 255, 0.5);
  border: none;
  padding: var(--space-2);
  border-radius: var(--radius-sm);
  cursor: pointer;
  transition: color var(--motion-fast), transform var(--motion-base) var(--ease-out);
}
.collapse-toggle:hover { color: white; }

/* Collapsed state — labels fade out, sidebar narrows */
.sidebar.is-collapsed .brand-text,
.sidebar.is-collapsed .nav-label,
.sidebar.is-collapsed .nav-heading {
  opacity: 0;
  pointer-events: none;
}
.sidebar.is-collapsed .collapse-toggle { transform: rotate(180deg); }

/* Mobile drawer */
@media (max-width: 768px) {
  .sidebar {
    position: fixed;
    inset: 0 auto 0 0;
    width: var(--sidebar-w-expanded);
    transform: translateX(-100%);
    transition: transform var(--motion-base) var(--ease-out);
    z-index: 50;
  }
  .sidebar.is-mobile-open { transform: translateX(0); }
}
</style>
```

---

## `App.vue` outer layout

```vue
<template>
  <div
    class="app-shell"
    :class="{ 'is-collapsed': isCollapsed, 'is-mobile-open': mobileOpen }"
  >
    <Sidebar
      :collapsed="isCollapsed"
      :mobile-open="mobileOpen"
      @toggle="toggleCollapse"
      @close-mobile="mobileOpen = false"
    />
    <button
      v-if="mobileOpen"
      class="mobile-scrim"
      @click="mobileOpen = false"
      aria-label="Close navigation"
    ></button>
    <main class="app-main">
      <FilterBar />
      <router-view />
    </main>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'
import Sidebar from '@/components/Sidebar.vue'
import FilterBar from '@/components/FilterBar.vue'

const isCollapsed = ref(false)
const mobileOpen = ref(false)

const toggleCollapse = () => {
  if (window.matchMedia('(max-width: 768px)').matches) {
    mobileOpen.value = !mobileOpen.value
  } else {
    isCollapsed.value = !isCollapsed.value
  }
}

onMounted(() => {
  const saved = localStorage.getItem('sidebar:collapsed')
  if (saved !== null) isCollapsed.value = saved === 'true'
})
watch(isCollapsed, (v) => localStorage.setItem('sidebar:collapsed', String(v)))
</script>

<style>
.app-shell {
  display: grid;
  grid-template-columns: var(--sidebar-w) 1fr;
  min-height: 100vh;
  transition: grid-template-columns var(--motion-base) var(--ease-out);
}
.app-shell.is-collapsed { --sidebar-w: var(--sidebar-w-collapsed); }
.app-main {
  background: var(--color-bg-app);
  min-width: 0;
  display: flex;
  flex-direction: column;
}
.mobile-scrim {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.5);
  border: none;
  z-index: 40;
}
@media (max-width: 768px) {
  .app-shell { grid-template-columns: 0 1fr; }
  .app-shell.is-mobile-open .mobile-scrim { display: block; }
}
</style>
```

---

## Notes

- **i18n keys referenced:** `nav.companyName`, `nav.primary`, `nav.more`, `nav.expand`, `nav.collapse`, plus per-route keys (`nav.overview`, `nav.inventory`, etc.). Add `nav.expand` / `nav.collapse` / `nav.more` if missing from your locale files.
- **Icons:** the inline SVGs above are line-icon style at 18×18 px with `currentColor` stroke — they inherit color from `.nav-link`. Swap for an icon-library `<component :is="Icon" />` if available.
- **Popover clipping:** `Sidebar.vue` uses `overflow: hidden` so labels can collapse cleanly. If your `ProfileMenu` or `LanguageSwitcher` opens a dropdown, render it via `<Teleport to="body">` to escape this clip.
- **No TypeScript:** template is JS; convert to TS by typing `defineProps` generically and the icon array as `{ to: string; label: string; icon: string }[]`.
