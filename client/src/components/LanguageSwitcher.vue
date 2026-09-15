<template>
  <div class="language-switcher">
    <button
      class="language-button"
      @click="toggleDropdown"
      @blur="handleBlur"
      :aria-label="t('language.selectLanguage')"
      :title="t('language.selectLanguage')"
    >
      <svg
        width="20"
        height="20"
        viewBox="0 0 20 20"
        fill="none"
        class="globe-icon"
      >
        <circle cx="10" cy="10" r="7.5" stroke="currentColor" stroke-width="1.5"/>
        <path d="M3 10H17" stroke="currentColor" stroke-width="1.5"/>
        <path d="M10 3C10 3 7.5 5.5 7.5 10C7.5 14.5 10 17 10 17" stroke="currentColor" stroke-width="1.5"/>
        <path d="M10 3C10 3 12.5 5.5 12.5 10C12.5 14.5 10 17 10 17" stroke="currentColor" stroke-width="1.5"/>
      </svg>
      <span class="language-label">{{ localeName }}</span>
      <svg
        class="chevron"
        :class="{ 'chevron-open': isDropdownOpen }"
        width="16"
        height="16"
        viewBox="0 0 16 16"
        fill="none"
      >
        <path d="M4 6L8 10L12 6" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
      </svg>
    </button>

    <div v-if="isDropdownOpen" class="dropdown-menu">
      <button
        v-for="locale in availableLocales"
        :key="locale"
        class="dropdown-item"
        :class="{ active: currentLocale === locale }"
        @mousedown.prevent="selectLanguage(locale)"
      >
        <span class="language-name">{{ getLanguageName(locale) }}</span>
        <svg
          v-if="currentLocale === locale"
          width="18"
          height="18"
          viewBox="0 0 18 18"
          fill="none"
          class="check-icon"
        >
          <path d="M4 9L7.5 12.5L14 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useI18n } from '../composables/useI18n'

const { t, currentLocale, setLocale, availableLocales, localeName } = useI18n()

const isDropdownOpen = ref(false)

const languageNames = {
  en: 'English',
  ja: '日本語'
}

const getLanguageName = (locale) => {
  return languageNames[locale] || locale
}

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value
}

const handleBlur = () => {
  // Delay to allow mousedown events on dropdown items to fire first
  setTimeout(() => {
    isDropdownOpen.value = false
  }, 200)
}

const selectLanguage = (locale) => {
  setLocale(locale)
  isDropdownOpen.value = false
}
</script>

<style scoped>
.language-switcher {
  position: relative;
}

.language-button {
  display: flex;
  align-items: center;
  gap: var(--space-2);
  width: 100%;
  justify-content: space-between;
  padding: var(--space-2) 0.875rem;
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: all var(--transition);
  font-family: inherit;
  font-size: var(--text-sm);
  color: var(--color-text-body);
}

.language-button:hover {
  background: var(--color-surface-sunken);
  border-color: var(--color-border-strong);
}

.globe-icon {
  color: var(--color-text-muted);
  flex-shrink: 0;
}

.language-label {
  flex: 1;
  min-width: 0;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  text-align: left;
  font-weight: 500;
}

.chevron {
  color: var(--color-text-muted);
  transition: transform var(--transition);
  flex-shrink: 0;
}

.chevron-open {
  transform: rotate(180deg);
}

.dropdown-menu {
  position: absolute;
  top: auto;
  bottom: calc(100% + var(--space-2));
  left: 0;
  right: auto;
  /* min() of 100% keeps the current expanded-sidebar width; the fixed 180px
     floor keeps the menu legible when the button itself is collapsed to 72px. */
  min-width: max(100%, 180px);
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-lg);
  z-index: var(--z-dropdown);
  overflow: hidden;
}

/* Collapsed sidebar - see the matching comment in App.vue's <style> block.
   Triggered independently by .sidebar-collapsed and the 1024px breakpoint. */
.app.sidebar-collapsed .language-button {
  justify-content: center;
}

.app.sidebar-collapsed .language-label,
.app.sidebar-collapsed .chevron {
  display: none;
}

@media (max-width: 1024px) {
  .language-button {
    justify-content: center;
  }

  .language-label,
  .chevron {
    display: none;
  }
}

.dropdown-item {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-3);
  padding: var(--space-3) var(--space-4);
  background: none;
  border: none;
  text-align: left;
  cursor: pointer;
  transition: background var(--transition-fast);
  font-family: inherit;
  font-size: var(--text-sm);
  font-weight: 500;
  color: var(--color-text-body);
  white-space: nowrap;
}

.dropdown-item:hover {
  background: var(--color-surface-sunken);
}

.dropdown-item.active {
  background: var(--color-primary-soft);
  color: var(--color-primary);
}

.language-name {
  flex: 1;
}

.check-icon {
  color: var(--color-primary);
  flex-shrink: 0;
}
</style>
