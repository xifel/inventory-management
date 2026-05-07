<template>
  <nav
    class="sidebar"
    :class="{ 'is-collapsed': collapsed, 'is-mobile-open': mobileOpen }"
    role="navigation"
    aria-label="Main navigation"
    @keydown.esc="$emit('close-mobile')"
  >
    <!-- Brand -->
    <div class="sidebar-brand">
      <div class="brand-mark">
        <svg width="28" height="28" viewBox="0 0 28 28" fill="none">
          <rect x="2" y="2" width="10" height="10" rx="2" fill="#2dd4cf"/>
          <rect x="16" y="2" width="10" height="10" rx="2" fill="rgba(45,212,207,0.5)"/>
          <rect x="2" y="16" width="10" height="10" rx="2" fill="rgba(45,212,207,0.5)"/>
          <rect x="16" y="16" width="10" height="10" rx="2" fill="#2dd4cf"/>
        </svg>
      </div>
      <div class="brand-text">
        <span class="brand-name">Catalyst Components</span>
        <span class="brand-sub">Inventory Management</span>
      </div>
    </div>

    <!-- Primary Nav -->
    <div class="sidebar-nav-section">
      <ul class="nav-list" role="list">
        <li>
          <router-link
            to="/"
            class="nav-item"
            exact-active-class="is-active"
            :title="collapsed ? t('nav.overview') : undefined"
          >
            <span class="nav-icon" aria-hidden="true">
              <!-- Grid / Dashboard -->
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
                <rect x="2" y="2" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
                <rect x="10" y="2" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
                <rect x="2" y="10" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
                <rect x="10" y="10" width="6" height="6" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <span class="nav-label">{{ t('nav.overview') }}</span>
          </router-link>
        </li>
        <li>
          <router-link
            to="/inventory"
            class="nav-item"
            active-class="is-active"
            :title="collapsed ? t('nav.inventory') : undefined"
          >
            <span class="nav-icon" aria-hidden="true">
              <!-- Box / Cube -->
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
                <path d="M9 2L15 5.5V12.5L9 16L3 12.5V5.5L9 2Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
                <path d="M9 2L9 16" stroke="currentColor" stroke-width="1.5"/>
                <path d="M3 5.5L15 5.5" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <span class="nav-label">{{ t('nav.inventory') }}</span>
          </router-link>
        </li>
        <li>
          <router-link
            to="/orders"
            class="nav-item"
            active-class="is-active"
            :title="collapsed ? t('nav.orders') : undefined"
          >
            <span class="nav-icon" aria-hidden="true">
              <!-- List -->
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
                <path d="M6 5H15" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M6 9H15" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M6 13H15" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
                <circle cx="3" cy="5" r="1" fill="currentColor"/>
                <circle cx="3" cy="9" r="1" fill="currentColor"/>
                <circle cx="3" cy="13" r="1" fill="currentColor"/>
              </svg>
            </span>
            <span class="nav-label">{{ t('nav.orders') }}</span>
          </router-link>
        </li>
        <li>
          <router-link
            to="/spending"
            class="nav-item"
            active-class="is-active"
            :title="collapsed ? t('nav.finance') : undefined"
          >
            <span class="nav-icon" aria-hidden="true">
              <!-- Dollar sign -->
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
                <path d="M9 2V16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M12.5 5.5C12.5 4.12 10.9 3 9 3C7.1 3 5.5 4.12 5.5 5.5C5.5 6.88 7.1 8 9 8C10.9 8 12.5 9.12 12.5 10.5C12.5 11.88 10.9 13 9 13C7.1 13 5.5 11.88 5.5 10.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
              </svg>
            </span>
            <span class="nav-label">{{ t('nav.finance') }}</span>
          </router-link>
        </li>
        <li>
          <router-link
            to="/demand"
            class="nav-item"
            active-class="is-active"
            :title="collapsed ? t('nav.demandForecast') : undefined"
          >
            <span class="nav-icon" aria-hidden="true">
              <!-- Trending up -->
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
                <path d="M2 13L7 8L10 11L15.5 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M12 5H15.5V8.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              </svg>
            </span>
            <span class="nav-label">{{ t('nav.demandForecast') }}</span>
          </router-link>
        </li>
      </ul>
    </div>

    <!-- Divider -->
    <div class="sidebar-divider"></div>

    <!-- Secondary Nav -->
    <div class="sidebar-nav-section">
      <ul class="nav-list" role="list">
        <li>
          <router-link
            to="/reports"
            class="nav-item"
            active-class="is-active"
            :title="collapsed ? 'Reports' : undefined"
          >
            <span class="nav-icon" aria-hidden="true">
              <!-- Bar chart -->
              <svg width="18" height="18" viewBox="0 0 18 18" fill="none">
                <rect x="2" y="10" width="4" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
                <rect x="7" y="6" width="4" height="10" rx="1" stroke="currentColor" stroke-width="1.5"/>
                <rect x="12" y="2" width="4" height="14" rx="1" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </span>
            <span class="nav-label">Reports</span>
          </router-link>
        </li>
      </ul>
    </div>

    <!-- Footer slot -->
    <div class="sidebar-footer">
      <slot name="footer" />
      <button
        class="collapse-toggle"
        @click="$emit('toggle')"
        :aria-label="collapsed ? 'Expand sidebar' : 'Collapse sidebar'"
        :title="collapsed ? 'Expand sidebar' : 'Collapse sidebar'"
      >
        <svg
          class="collapse-icon"
          :class="{ 'is-rotated': collapsed }"
          width="18"
          height="18"
          viewBox="0 0 18 18"
          fill="none"
        >
          <path d="M11 4L6 9L11 14" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </div>
  </nav>
</template>

<script setup>
import { useI18n } from '../composables/useI18n'

const { t } = useI18n()

defineProps({
  collapsed: {
    type: Boolean,
    default: false
  },
  mobileOpen: {
    type: Boolean,
    default: false
  }
})

defineEmits(['toggle', 'close-mobile'])
</script>

<style scoped>
.sidebar {
  background: var(--color-sidebar);
  display: flex;
  flex-direction: column;
  height: 100vh;
  position: sticky;
  top: 0;
  overflow: hidden;
  width: var(--sidebar-w);
  transition: width var(--motion-base) var(--ease-out);
  z-index: 50;
  flex-shrink: 0;
}

/* Mobile: fixed drawer */
@media (max-width: 768px) {
  .sidebar {
    position: fixed;
    left: 0;
    top: 0;
    bottom: 0;
    width: var(--sidebar-w-expanded);
    transform: translateX(-100%);
    transition: transform var(--motion-base) var(--ease-out);
    z-index: 50;
  }
  .sidebar.is-mobile-open {
    transform: translateX(0);
  }
}

/* ── Brand ── */
.sidebar-brand {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 1.25rem 1rem 1rem;
  border-bottom: 1px solid rgba(255,255,255,0.07);
  flex-shrink: 0;
  min-height: 64px;
}

.brand-mark {
  flex-shrink: 0;
  display: flex;
  align-items: center;
}

.brand-text {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: opacity var(--motion-base), width var(--motion-base);
  white-space: nowrap;
}

.brand-name {
  font-size: 0.875rem;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: -0.01em;
  line-height: 1.2;
}

.brand-sub {
  font-size: 0.68rem;
  color: var(--color-fg-on-dark);
  opacity: 0.6;
  margin-top: 1px;
}

/* ── Nav sections ── */
.sidebar-nav-section {
  padding: 0.5rem 0.5rem;
}

.nav-list {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 1px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  border-radius: var(--radius-sm);
  color: var(--color-fg-on-dark);
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  transition: background var(--motion-fast), color var(--motion-fast);
  white-space: nowrap;
  overflow: hidden;
}

.nav-item:hover {
  background: var(--color-sidebar-hover);
  color: #ffffff;
}

.nav-item.is-active {
  background: var(--color-sidebar-active);
  color: var(--color-sidebar-accent);
}

.nav-icon {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 18px;
  height: 18px;
}

.nav-label {
  transition: opacity var(--motion-base);
}

/* ── Divider ── */
.sidebar-divider {
  height: 1px;
  background: rgba(255,255,255,0.07);
  margin: 0.25rem 0.5rem;
}

/* ── Footer ── */
.sidebar-footer {
  margin-top: auto;
  padding: 0.5rem 0.5rem;
  border-top: 1px solid rgba(255,255,255,0.07);
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  overflow: visible;
  flex-shrink: 0;
}

.collapse-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 0.625rem;
  background: transparent;
  border: none;
  border-radius: var(--radius-sm);
  color: var(--color-fg-on-dark);
  cursor: pointer;
  transition: background var(--motion-fast), color var(--motion-fast);
}

.collapse-toggle:hover {
  background: var(--color-sidebar-hover);
  color: #ffffff;
}

.collapse-icon {
  transition: transform var(--motion-base) var(--ease-out);
  flex-shrink: 0;
}

.collapse-icon.is-rotated {
  transform: rotate(180deg);
}

/* ── Collapsed state ── */
.sidebar.is-collapsed .brand-text,
.sidebar.is-collapsed .nav-label {
  opacity: 0;
  pointer-events: none;
  width: 0;
}
</style>
