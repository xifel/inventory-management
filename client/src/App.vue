<template>
  <div
    class="app-shell"
    :class="{
      'is-collapsed': isCollapsed,
      'is-mobile-open': mobileOpen
    }"
  >
    <Sidebar
      :collapsed="isCollapsed"
      :mobile-open="mobileOpen"
      @toggle="toggleSidebar"
      @close-mobile="mobileOpen = false"
    >
      <template #footer>
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
        <LanguageSwitcher />
      </template>
    </Sidebar>

    <button
      v-if="mobileOpen"
      class="mobile-scrim"
      @click="mobileOpen = false"
      aria-label="Close navigation"
    ></button>

    <main class="app-main">
      <FilterBar />
      <div class="main-content">
        <router-view />
      </div>
    </main>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, computed, onMounted, watch } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import Sidebar from './components/Sidebar.vue'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    Sidebar,
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const apiTasks = ref([])

    // Sidebar state
    const isCollapsed = ref(false)
    const mobileOpen = ref(false)

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)
        if (isMockTask) {
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)
        if (mockTask) {
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    const toggleSidebar = () => {
      if (window.innerWidth < 768) {
        mobileOpen.value = !mobileOpen.value
      } else {
        isCollapsed.value = !isCollapsed.value
      }
    }

    onMounted(() => {
      loadTasks()
      // Restore sidebar collapsed state from localStorage
      const stored = localStorage.getItem('sidebar-collapsed')
      if (stored !== null) {
        isCollapsed.value = stored === 'true'
      }
    })

    watch(isCollapsed, (val) => {
      localStorage.setItem('sidebar-collapsed', String(val))
    })

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      isCollapsed,
      mobileOpen,
      toggleSidebar
    }
  }
}
</script>

<style>
/* ═══════════════════════════════════════════
   DESIGN TOKENS
═══════════════════════════════════════════ */
:root {
  --font-sans: 'DM Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  --font-mono: 'DM Mono', ui-monospace, SFMono-Regular, Menlo, monospace;

  /* Sidebar */
  --color-sidebar:        #0c3d4a;
  --color-sidebar-hover:  rgba(255,255,255,0.07);
  --color-sidebar-active: rgba(45,212,207,0.13);
  --color-sidebar-accent: #2dd4cf;
  --sidebar-w-expanded:   256px;
  --sidebar-w-collapsed:  64px;
  --sidebar-w:            var(--sidebar-w-expanded);

  /* Surfaces */
  --color-bg-app:         #f0f4f7;
  --color-bg-surface:     #ffffff;
  --color-bg-sunken:      #f7f9fb;

  /* Text */
  --color-fg-primary:     #0d1b24;
  --color-fg-secondary:   #4a6272;
  --color-fg-muted:       #8fa3b0;
  --color-fg-on-dark:     #cfe8f0;

  /* Accent */
  --color-accent:         #0891b2;
  --color-accent-hover:   #0e7490;
  --color-accent-soft:    #e0f9fc;

  /* Borders */
  --color-border:         #d8e4ea;
  --color-border-soft:    #ecf2f5;

  /* Status */
  --color-success:        #059669;
  --color-success-soft:   #d1fae5;
  --color-success-text:   #065f46;
  --color-warning:        #d97706;
  --color-warning-soft:   #fef3c7;
  --color-warning-text:   #92400e;
  --color-danger:         #dc2626;
  --color-danger-soft:    #fee2e2;
  --color-danger-text:    #991b1b;
  --color-info:           #0891b2;
  --color-info-soft:      #e0f9fc;
  --color-info-text:      #164e63;

  /* Spacing */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-5: 1.5rem;
  --space-6: 2rem;
  --space-7: 3rem;

  /* Radius */
  --radius-sm:   6px;
  --radius-md:   10px;
  --radius-lg:   16px;
  --radius-pill: 999px;

  /* Shadows */
  --shadow-card:    0 1px 3px rgba(13,27,36,0.05), 0 4px 16px rgba(13,27,36,0.04);
  --shadow-elevate: 0 8px 32px rgba(13,27,36,0.12);
  --shadow-pop:     0 4px 16px rgba(13,27,36,0.10);

  /* Motion */
  --motion-fast: 120ms;
  --motion-base: 200ms;
  --ease-out:    cubic-bezier(0.2, 0.8, 0.2, 1);
}
@media (prefers-reduced-motion: reduce) {
  :root { --motion-fast: 0ms; --motion-base: 0ms; }
}

/* ═══════════════════════════════════════════
   RESET + BASE
═══════════════════════════════════════════ */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { font-size: 16px; }
body {
  font-family: var(--font-sans);
  background: var(--color-bg-app);
  color: var(--color-fg-primary);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  line-height: 1.5;
}

/* ═══════════════════════════════════════════
   APP SHELL
═══════════════════════════════════════════ */
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
  background: rgba(12,61,74,0.45);
  backdrop-filter: blur(2px);
  border: none;
  z-index: 40;
  cursor: pointer;
}
@media (max-width: 768px) {
  .app-shell { grid-template-columns: 0 1fr; }
  .app-shell.is-mobile-open .mobile-scrim { display: block; }
}

/* ═══════════════════════════════════════════
   MAIN CONTENT AREA
═══════════════════════════════════════════ */
.main-content {
  padding: var(--space-6);
  max-width: 1440px;
  width: 100%;
  flex: 1;
}

/* ═══════════════════════════════════════════
   PAGE HEADER
═══════════════════════════════════════════ */
.page-header {
  margin-bottom: var(--space-5);
}
.page-header h2 {
  font-size: 1.625rem;
  font-weight: 700;
  color: var(--color-fg-primary);
  letter-spacing: -0.02em;
  line-height: 1.2;
  margin-bottom: 0.25rem;
}
.page-header p {
  color: var(--color-fg-secondary);
  font-size: 0.9rem;
}

/* ═══════════════════════════════════════════
   STAT CARDS GRID
═══════════════════════════════════════════ */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: var(--space-4);
  margin-bottom: var(--space-5);
}
.stat-card {
  background: var(--color-bg-surface);
  padding: var(--space-5);
  border-radius: var(--radius-md);
  border: 1px solid var(--color-border);
  box-shadow: var(--shadow-card);
  transition: border-color var(--motion-fast), box-shadow var(--motion-fast);
}
.stat-card:hover {
  border-color: #c0d0d8;
  box-shadow: 0 2px 8px rgba(13,27,36,0.08), 0 8px 24px rgba(13,27,36,0.06);
}
.stat-label {
  font-size: 0.72rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.07em;
  color: var(--color-fg-secondary);
  margin-bottom: var(--space-3);
}
.stat-value {
  font-size: 2rem;
  font-weight: 700;
  color: var(--color-fg-primary);
  letter-spacing: -0.03em;
  font-family: var(--font-mono);
}
.stat-card.warning .stat-value { color: var(--color-warning); }
.stat-card.success .stat-value { color: var(--color-success); }
.stat-card.danger .stat-value  { color: var(--color-danger);  }
.stat-card.info .stat-value    { color: var(--color-accent);  }

/* ═══════════════════════════════════════════
   CARDS
═══════════════════════════════════════════ */
.card {
  background: var(--color-bg-surface);
  border-radius: var(--radius-md);
  padding: var(--space-5);
  border: 1px solid var(--color-border);
  box-shadow: var(--shadow-card);
  margin-bottom: var(--space-4);
}
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--space-4);
  padding-bottom: var(--space-4);
  border-bottom: 1px solid var(--color-border-soft);
}
.card-title {
  font-size: 1rem;
  font-weight: 700;
  color: var(--color-fg-primary);
  letter-spacing: -0.015em;
}

/* ═══════════════════════════════════════════
   TABLES
═══════════════════════════════════════════ */
.table-container {
  overflow-x: auto;
  margin: 0 calc(-1 * var(--space-5));
  padding: 0 var(--space-5);
}
table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.875rem;
}
thead {
  background: var(--color-bg-sunken);
  border-top: 1px solid var(--color-border-soft);
  border-bottom: 1px solid var(--color-border);
}
th {
  text-align: left;
  padding: 0.625rem var(--space-3);
  font-size: 0.7rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.07em;
  color: var(--color-fg-secondary);
  white-space: nowrap;
}
td {
  padding: 0.625rem var(--space-3);
  border-top: 1px solid var(--color-border-soft);
  color: var(--color-fg-primary);
  font-size: 0.875rem;
}
td.mono, td .id-cell, td .sku-cell {
  font-family: var(--font-mono);
  font-size: 0.82rem;
  color: var(--color-fg-secondary);
}
td .number-cell {
  font-family: var(--font-mono);
}
tbody tr { transition: background-color var(--motion-fast); }
tbody tr:hover { background: var(--color-bg-sunken); }

/* ═══════════════════════════════════════════
   BADGES
═══════════════════════════════════════════ */
.badge {
  display: inline-flex;
  align-items: center;
  padding: 0.25rem 0.625rem;
  border-radius: var(--radius-pill);
  font-size: 0.7rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  line-height: 1;
}
.badge.success    { background: var(--color-success-soft); color: var(--color-success-text); }
.badge.warning    { background: var(--color-warning-soft); color: var(--color-warning-text); }
.badge.danger     { background: var(--color-danger-soft);  color: var(--color-danger-text);  }
.badge.info       { background: var(--color-info-soft);    color: var(--color-info-text);    }
.badge.increasing { background: var(--color-success-soft); color: var(--color-success-text); }
.badge.decreasing { background: var(--color-danger-soft);  color: var(--color-danger-text);  }
.badge.stable     { background: #e0e7ff; color: #3730a3; }
.badge.high       { background: var(--color-danger-soft);  color: var(--color-danger-text);  }
.badge.medium     { background: var(--color-warning-soft); color: var(--color-warning-text); }
.badge.low        { background: var(--color-info-soft);    color: var(--color-info-text);    }

/* ═══════════════════════════════════════════
   LOADING + ERROR STATES
═══════════════════════════════════════════ */
.loading {
  text-align: center;
  padding: var(--space-7);
  color: var(--color-fg-muted);
  font-size: 0.9rem;
}
.error {
  background: var(--color-danger-soft);
  border: 1px solid #fca5a5;
  color: var(--color-danger-text);
  padding: var(--space-4);
  border-radius: var(--radius-sm);
  margin: var(--space-4) 0;
  font-size: 0.875rem;
}
</style>
