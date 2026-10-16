# Vue 3 reference (dev-workflow)

Read this before writing or modifying a `.vue` file, a composable or a store.

## Toolchain

Look at: `package.json` scripts, `vite.config.*`, `tsconfig.json`, `vue-tsc`
presence, eslint config, lockfile for the package manager.

- Typical check: `npx vue-tsc --noEmit`, then the project's `lint` / `format` /
  `test`.
- Vue 3 + `<script setup>` is the baseline; follow the existing file's style if
  you are editing a legacy Options-API component.

## Component rules

- Always `<script setup lang="ts">` and Composition API for new code. SFC block
  order: `<script setup>` -> `<template>` -> `<style scoped>`.
- Typed APIs only: `defineProps<Props>()` with `withDefaults`,
  `defineEmits<{ ... }>()`, `defineModel<T>()` for `v-model`.
- **Props are read-only** - never mutate them; emit an event or update local
  state through the store instead.
- **No business logic in components.** Extract it into `src/composables/useX.ts`
  with explicit `Ref<T>` / `ComputedRef<T>` return types. Shared state lives in a
  Pinia store per feature, mutated only through its actions.
- `computed` for derived data; `watch`/`watchEffect` **only** for side effects,
  with explicit sources and cleanup. No deep watchers on large structures.
- Components stay small and presentational: props down, events up. Beyond two
  levels of prop drilling use `provide`/`inject` or the store.
- Direct DOM access only through template refs, never `document.*`.

## Template rules

- Always `:key` with a stable unique id - never the loop index.
- Never `v-if` and `v-for` on the same element; `v-show` only for frequent
  toggling; `v-html` only on sanitised content.
- Accessibility is part of done: semantic elements, `label`/`for`, keyboard
  handlers, `aria-*` where needed, `alt` on images.

## Style and naming

- `<style scoped>`, no `!important`, use design tokens/CSS variables; inline
  styles only for dynamic values.
- PascalCase multi-word file names (`UserCard.vue`, not `Card.vue`), `useFoo`
  composables, `useFooStore` stores, `Base`/`The` prefixes if the project follows
  the Vue style guide.
- Layout: `components/` (presentation) + `composables/` (logic) + `stores/`
  (shared state) + `api/` (I/O), or whatever the project already uses. Split an
  SFC past ~200-300 lines into child components.

## Test

- `@vue/test-utils` + vitest, driving the component through user interactions
  (find by role/text, `await` DOM updates) - not through component internals.
