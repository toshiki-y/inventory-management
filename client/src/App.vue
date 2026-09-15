<template>
  <div class="app" :class="{ 'sidebar-collapsed': sidebarCollapsed }">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <div class="sidebar-brand-text">
          <h1 class="sidebar-brand-name">{{ t('nav.companyName') }}</h1>
          <p class="sidebar-brand-sub" :title="t('nav.subtitle')">{{ t('nav.subtitle') }}</p>
        </div>
        <span
          class="sidebar-brand-mark"
          :title="t('nav.companyName')"
          :aria-label="t('nav.companyName')"
        >{{ brandMark }}</span>
        <button
          type="button"
          class="sidebar-toggle"
          @click="toggleSidebar"
          :aria-expanded="!sidebarCollapsed"
          :aria-label="sidebarCollapsed ? t('nav.expandSidebar') : t('nav.collapseSidebar')"
          :title="sidebarCollapsed ? t('nav.expandSidebar') : t('nav.collapseSidebar')"
        >
          <svg
            viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
            stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"
            class="sidebar-toggle-icon" :class="{ collapsed: sidebarCollapsed }"
          >
            <path d="M12.5 5 7.5 10l5 5" />
          </svg>
        </button>
      </div>

      <nav class="sidebar-nav" :aria-label="t('nav.subtitle')">
        <router-link
          class="nav-item"
          to="/"
          :class="{ active: $route.path === '/' }"
          :aria-current="$route.path === '/' ? 'page' : undefined"
          :title="t('nav.overview')"
          :aria-label="t('nav.overview')"
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

        <router-link
          class="nav-item"
          to="/inventory"
          :class="{ active: $route.path === '/inventory' }"
          :aria-current="$route.path === '/inventory' ? 'page' : undefined"
          :title="t('nav.inventory')"
          :aria-label="t('nav.inventory')"
        >
          <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
               stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M2.5 6.5 10 2.5l7.5 4v7L10 17.5l-7.5-4v-7Z" />
            <path d="M2.5 6.5 10 10.5l7.5-4M10 10.5v7" />
          </svg>
          <span class="nav-item-label">{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link
          class="nav-item"
          to="/orders"
          :class="{ active: $route.path === '/orders' }"
          :aria-current="$route.path === '/orders' ? 'page' : undefined"
          :title="t('nav.orders')"
          :aria-label="t('nav.orders')"
        >
          <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
               stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M7 3.5H5.5A1.5 1.5 0 0 0 4 5v11a1.5 1.5 0 0 0 1.5 1.5h9A1.5 1.5 0 0 0 16 16V5a1.5 1.5 0 0 0-1.5-1.5H13" />
            <rect x="7" y="2" width="6" height="3" rx="1" />
            <path d="M7 9h6M7 12.5h4" />
          </svg>
          <span class="nav-item-label">{{ t('nav.orders') }}</span>
        </router-link>

        <router-link
          class="nav-item"
          to="/spending"
          :class="{ active: $route.path === '/spending' }"
          :aria-current="$route.path === '/spending' ? 'page' : undefined"
          :title="t('nav.finance')"
          :aria-label="t('nav.finance')"
        >
          <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
               stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <circle cx="10" cy="10" r="7.5" />
            <path d="M12.5 7.5H9a1.75 1.75 0 0 0 0 3.5h2a1.75 1.75 0 0 1 0 3.5H7.5M10 5.5v1.5M10 13v1.5" />
          </svg>
          <span class="nav-item-label">{{ t('nav.finance') }}</span>
        </router-link>

        <router-link
          class="nav-item"
          to="/demand"
          :class="{ active: $route.path === '/demand' }"
          :aria-current="$route.path === '/demand' ? 'page' : undefined"
          :title="t('nav.demandForecast')"
          :aria-label="t('nav.demandForecast')"
        >
          <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
               stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M2.5 13.5 7 9l3 3 4.5-5.5" />
            <path d="M11.5 6.5h3.5V10" />
            <path d="M2.5 17.5h15" stroke-dasharray="2 2" />
          </svg>
          <span class="nav-item-label">{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link
          class="nav-item"
          to="/reports"
          :class="{ active: $route.path === '/reports' }"
          :aria-current="$route.path === '/reports' ? 'page' : undefined"
          :title="t('nav.reports')"
          :aria-label="t('nav.reports')"
        >
          <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5"
               stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M11.5 2.5H6A1.5 1.5 0 0 0 4.5 4v12A1.5 1.5 0 0 0 6 17.5h8a1.5 1.5 0 0 0 1.5-1.5V6.5l-4-4Z" />
            <path d="M11.5 2.5v4h4M7.5 11h5M7.5 14h3" />
          </svg>
          <span class="nav-item-label">{{ t('nav.reports') }}</span>
        </router-link>
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
import { ref, onMounted, computed } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

const SIDEBAR_COLLAPSED_KEY = 'app-sidebar-collapsed'

// Storage can throw (private browsing, disabled storage, quota); the sidebar
// must still render either way, so every read/write is wrapped.
const readStoredSidebarCollapsed = () => {
  try {
    return localStorage.getItem(SIDEBAR_COLLAPSED_KEY) === 'true'
  } catch (err) {
    return false
  }
}

const writeStoredSidebarCollapsed = (value) => {
  try {
    localStorage.setItem(SIDEBAR_COLLAPSED_KEY, String(value))
  } catch (err) {
    // Ignore - collapsed state still works for the current session
  }
}

export default {
  name: 'App',
  components: {
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
    const sidebarCollapsed = ref(readStoredSidebarCollapsed())

    // First character of the localized company name - used as a compact
    // mark in the sidebar brand block when the sidebar is collapsed.
    const brandMark = computed(() => {
      const name = t('nav.companyName')
      return name ? name.trim().charAt(0) : ''
    })

    const toggleSidebar = () => {
      sidebarCollapsed.value = !sidebarCollapsed.value
      writeStoredSidebarCollapsed(sidebarCollapsed.value)
    }

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
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
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

    onMounted(loadTasks)

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      sidebarCollapsed,
      toggleSidebar,
      brandMark
    }
  }
}
</script>

<style>
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

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--color-bg);
  color: var(--color-text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

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

/* Sidebar */

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
  display: flex;
  align-items: center;
  gap: var(--space-2);
  padding: var(--space-1) var(--space-3);
  min-width: 0;
}

.sidebar-brand-text {
  flex: 1;
  min-width: 0;
}

.sidebar-brand-name {
  font-size: var(--text-sm);
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.01em;
  line-height: 1.25;
}

.sidebar-brand-sub {
  font-size: var(--text-xs);
  color: var(--color-text-muted);
  line-height: 1.35;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Compact stand-in for the brand name when the sidebar is collapsed.
   Hidden by default; shown only by the collapsed rules below. */
.sidebar-brand-mark {
  display: none;
  font-size: var(--text-lg);
  font-weight: 700;
  color: var(--color-text);
  text-align: center;
}

.sidebar-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  flex-shrink: 0;
  background: none;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  color: var(--color-text-muted);
  cursor: pointer;
  transition: background var(--transition-fast), color var(--transition-fast);
}

.sidebar-toggle:hover {
  background: var(--color-surface-hover);
  color: var(--color-text);
}

.sidebar-toggle:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.sidebar-toggle svg {
  width: 16px;
  height: 16px;
}

.sidebar-toggle-icon {
  transition: transform var(--transition-fast);
}

.sidebar-toggle-icon.collapsed {
  transform: rotate(180deg);
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

/* Collapsible sidebar
   Redefining the width token is the whole mechanism - the grid needs no change. */
.app.sidebar-collapsed {
  --sidebar-width: var(--sidebar-width-collapsed);
}

/* Collapsed presentation is triggered from two independent places:
   1) .sidebar-collapsed, toggled manually and persisted to localStorage
   2) the 1024px breakpoint below, which applies regardless of the toggle
   Both must hide the same labels and re-center the same items, so the same
   declarations are listed under each trigger. Keep them in sync. */
.app.sidebar-collapsed .sidebar-brand-text,
.app.sidebar-collapsed .sidebar-brand-sub {
  display: none;
}

.app.sidebar-collapsed .sidebar-brand-mark {
  display: block;
}

.app.sidebar-collapsed .sidebar-brand {
  flex-direction: column;
  gap: var(--space-2);
}

.app.sidebar-collapsed .nav-item {
  justify-content: center;
  padding: var(--space-2);
}

.app.sidebar-collapsed .nav-item-label {
  display: none;
}

@media (max-width: 1024px) {
  .app {
    --sidebar-width: var(--sidebar-width-collapsed);
  }

  .sidebar-brand-text,
  .sidebar-brand-sub {
    display: none;
  }

  .sidebar-brand-mark {
    display: block;
  }

  .sidebar-brand {
    flex-direction: column;
    gap: var(--space-2);
  }

  .nav-item {
    justify-content: center;
    padding: var(--space-2);
  }

  .nav-item-label {
    display: none;
  }
}

/* Global primitives */

.page-header {
  margin-bottom: var(--space-4);
}

.page-header h2 {
  font-size: var(--text-2xl);
  font-weight: 700;
  color: var(--color-text);
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: var(--color-text-muted);
  font-size: var(--text-sm);
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: var(--space-5);
  margin-bottom: var(--space-5);
}

.stat-card {
  background: var(--color-surface);
  padding: var(--space-5);
  border-radius: var(--radius-lg);
  border: 1px solid var(--color-border);
  transition: all var(--transition);
}

.stat-card:hover {
  border-color: var(--color-border-strong);
  box-shadow: var(--shadow-md);
}

.stat-label {
  color: var(--color-text-muted);
  font-size: var(--text-sm);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: var(--text-3xl);
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value {
  color: var(--color-warning);
}

.stat-card.success .stat-value {
  color: var(--color-success);
}

.stat-card.danger .stat-value {
  color: var(--color-danger);
}

.stat-card.info .stat-value {
  color: var(--color-primary);
}

.card {
  background: var(--color-surface);
  border-radius: var(--radius-lg);
  padding: var(--space-5);
  border: 1px solid var(--color-border);
  margin-bottom: var(--space-5);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--space-4);
  padding-bottom: 0.875rem;
  border-bottom: 1px solid var(--color-border);
}

.card-title {
  font-size: var(--text-lg);
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.025em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--color-surface-sunken);
  border-top: 1px solid var(--color-border);
  border-bottom: 1px solid var(--color-border);
}

th {
  text-align: left;
  padding: var(--space-2) var(--space-3);
  font-weight: 600;
  color: var(--color-text-secondary);
  font-size: var(--text-xs);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: var(--space-2) var(--space-3);
  border-top: 1px solid var(--color-border-subtle);
  color: var(--color-text-body);
  font-size: var(--text-sm);
}

tbody tr {
  transition: background-color var(--transition-fast);
}

tbody tr:hover {
  background: var(--color-surface-sunken);
}

.badge {
  display: inline-block;
  padding: 0.313rem var(--space-3);
  border-radius: var(--radius-sm);
  font-size: var(--text-xs);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: var(--color-success-soft);
  color: var(--color-on-success-soft);
}

.badge.warning {
  background: var(--color-warning-soft);
  color: var(--color-on-warning-soft);
}

.badge.danger {
  background: var(--color-danger-border);
  color: var(--color-danger-strong);
}

.badge.info {
  background: var(--color-primary-soft-strong);
  color: var(--color-primary-strong);
}

.badge.increasing {
  background: var(--color-success-soft);
  color: var(--color-on-success-soft);
}

.badge.decreasing {
  background: var(--color-danger-border);
  color: var(--color-danger-strong);
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: var(--color-danger-border);
  color: var(--color-danger-strong);
}

.badge.medium {
  background: var(--color-warning-soft);
  color: var(--color-on-warning-soft);
}

.badge.low {
  background: var(--color-primary-soft-strong);
  color: var(--color-primary-strong);
}

.loading {
  text-align: center;
  padding: var(--space-12);
  color: var(--color-text-muted);
  font-size: var(--text-sm);
}

.error {
  background: var(--color-danger-soft);
  border: 1px solid var(--color-danger-border);
  color: var(--color-danger-strong);
  padding: var(--space-4);
  border-radius: var(--radius-md);
  margin: var(--space-4) 0;
  font-size: var(--text-sm);
}
</style>
