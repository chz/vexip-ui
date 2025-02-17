<script setup lang="ts">
import { computed } from 'vue'

import { useI18n } from 'vue-i18n'

import { useData } from 'vitepress'
import { ChevronLeft, ChevronRight } from '@vexip-ui/icons'
import { flatTree } from '@vexip-ui/utils'
import { ensureStartingSlash, removeExt } from '../../shared'

import type { ThemeConfig } from '../types'

const { theme, page, frontmatter } = useData<ThemeConfig>()
const { t, locale } = useI18n({ useScope: 'global' })

const path = computed(() => ensureStartingSlash(removeExt(page.value.relativePath)))
const candidates = computed(() => {
  const config = theme.value.asideMenus || {}

  let menus

  for (const key of Object.keys(config)) {
    if (path.value.startsWith(`/${locale.value}${key}`)) {
      menus = config[key]
      break
    }
  }

  if (!menus) return []

  return flatTree(menus, {
    childField: 'items',
    depthFirst: true,
    injectId: false,
    filter: menu => !!menu.link
  })
})
const index = computed(() => {
  return candidates.value.findIndex(item => path.value.startsWith(`/${locale.value}${item.link}`))
})
const prev = computed(() => {
  const prev = frontmatter.value.prev

  return prev === false
    ? undefined
    : {
        i18n:
          (typeof prev === 'string' ? prev : typeof prev === 'object' ? prev.i18n : undefined) ??
          candidates.value[index.value - 1]?.i18n,
        link:
          (typeof prev === 'object' ? prev.link : undefined) ??
          candidates.value[index.value - 1]?.link,
        origin:
          (typeof prev === 'object' ? prev.origin : undefined) ??
          candidates.value[index.value - 1]?.origin
      }
})
const next = computed(() => {
  const next = frontmatter.value.next

  return next === false
    ? undefined
    : {
        i18n:
          (typeof next === 'string' ? next : typeof next === 'object' ? next.i18n : undefined) ??
          candidates.value[index.value + 1]?.i18n,
        link:
          (typeof next === 'object' ? next.link : undefined) ??
          candidates.value[index.value + 1]?.link,
        origin:
          (typeof next === 'object' ? next.origin : undefined) ??
          candidates.value[index.value + 1]?.origin
      }
})
</script>

<template>
  <div v-if="prev?.link || next?.link" class="page-links">
    <Linker
      v-if="prev?.link"
      class="page-links__prev"
      type="primary"
      :to="`/${locale}${prev.link}`"
      target="_self"
      :icon="ChevronLeft"
    >
      <span>{{ t(prev.i18n) }}</span>
      <span v-if="prev.origin && locale !== 'en-US'" style="margin-left: 6px">
        {{ prev.origin }}
      </span>
    </Linker>
    <span role="none" style="flex: auto"></span>
    <Linker
      v-if="next?.link"
      class="page-links__next"
      type="primary"
      :to="`/${locale}${next.link}`"
      target="_self"
    >
      <span>{{ t(next.i18n) }}</span>
      <span v-if="next.origin && locale !== 'en-US'" style="margin-left: 6px">
        {{ next.origin }}
      </span>
      <Icon :icon="ChevronRight" style="padding-top: 1px; margin-left: 3px"></Icon>
    </Linker>
  </div>
</template>

<style lang="scss">
.page-links {
  display: flex;
  align-items: center;
  height: 72px;
  padding: 16px 0;

  .vxp-linker {
    height: 100%;

    &__icon {
      padding-top: 1px;
    }
  }
}
</style>
