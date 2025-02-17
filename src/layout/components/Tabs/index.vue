<template>
  <div v-if="appStore.tab" class="tabs">
    <a-tabs
      editable
      hide-content
      size="medium"
      :type="appStore.tabMode"
      :active-key="route.path"
      @tab-click="handleTabClick($event as string)"
      @delete="tabsStore.closeCurrent($event as string)"
    >
      <a-tab-pane
        v-for="item of tabsStore.tabList"
        :key="item.path"
        :title="(item.meta?.title as string)"
        :closable="Boolean(!item.meta?.affix)"
      >
      </a-tab-pane>
      <template #extra>
        <a-dropdown trigger="hover">
          <MagicIcon class="gi_mr"></MagicIcon>
          <template #content>
            <a-doption @click="tabsStore.closeCurrent(route.path)">
              <template #icon><icon-close /></template>
              <template #default>关闭当前</template>
            </a-doption>
            <a-doption @click="tabsStore.closeRight(route.path)">
              <template #icon><icon-close /></template>
              <template #default>关闭右侧</template>
            </a-doption>
            <a-doption @click="tabsStore.closeOther(route.path)">
              <template #icon><icon-eraser /></template>
              <template #default>关闭其他</template>
            </a-doption>
            <a-doption @click="tabsStore.closeAll">
              <template #icon><icon-minus /></template>
              <template #default>关闭全部</template>
            </a-doption>
          </template>
        </a-dropdown>
      </template>
    </a-tabs>
  </div>
</template>

<script setup lang="ts">
import type { RouteLocationNormalized } from 'vue-router'
import MagicIcon from './MagicIcon.vue'
import { useAppStore, useTabsStore } from '@/stores'

defineOptions({ name: 'Tabs' })
const route = useRoute()
const router = useRouter()
const appStore = useAppStore()
const tabsStore = useTabsStore()

// 重置, 同时把 affix: true 的路由筛选出来
tabsStore.init()

// 路由发生改变触发
const handleRouteChange = () => {
  const item = { ...route } as unknown as RouteLocationNormalized
  tabsStore.addTabItem(toRaw(item))
  tabsStore.addCacheItem(toRaw(item))
  // console.log('路由对象', toRaw(item))
  // console.log('tagList', toRaw(tabsStore.tabList))
  // console.log('cacheList', toRaw(tabsStore.cacheList))
}
handleRouteChange()

// 监听路由变化
watch(
  () => route.fullPath,
  () => {
    handleRouteChange()
  }
)

// 点击页签
const handleTabClick = (key: string) => {
  const obj = tabsStore.tabList.find((i) => i.path === key)
  obj ? router.push(obj.fullPath || obj.path) : router.push(key)
}
</script>

<style lang="scss" scoped>
:deep(.arco-tabs-nav-tab) {
  .arco-tabs-tab {
    border-bottom-color: transparent !important;
    svg {
      width: 0;
      transition: all 0.15s;
    }
    &:hover {
      svg {
        width: 1em;
      }
    }
  }
  &:not(.arco-tabs-nav-tab-scroll) {
    .arco-tabs-tab:first-child {
      border-left: 0;
    }
  }
}

:deep(.arco-dropdown-option-icon) {
  color: var(--color-text-3);
}

.tabs {
  padding-top: 5px;
  background-color: var(--color-bg-1);
}
</style>
