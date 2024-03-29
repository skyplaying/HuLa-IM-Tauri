<template>
  <n-flex vertical :size="6" class="tray">
    <n-flex vertical :size="6">
      <n-flex
        v-for="(item, index) in statusItem.slice(0, 6)"
        :key="index"
        align="center"
        :size="10"
        @click="toggleStatus(item.url, item.title)"
        class="p-6px rounded-4px hover:bg-[--tray-hover]">
        <img class="size-14px" :src="item.url" alt="" />
        <span>{{ item.title }}</span>
      </n-flex>
      <n-flex
        @click="createWebviewWindow('在线状态', 'onlineStatus', 320, 480)"
        align="center"
        :size="10"
        class="p-6px rounded-4px hover:bg-[--tray-hover]">
        <svg class="size-14px"><use href="#more"></use></svg>
        <span>更多状态</span>
      </n-flex>

      <component :is="division" />
      <n-flex align="center" :size="10" class="p-[8px_6px] rounded-4px hover:bg-[--tray-hover]">
        <span>打开所有声音</span>
      </n-flex>

      <component :is="division" />
      <n-flex
        @click="checkWinExist('home')"
        align="center"
        :size="10"
        class="p-[8px_6px] rounded-4px hover:bg-[--tray-hover]">
        <span>打开主面板</span>
      </n-flex>

      <component :is="division" />
      <n-flex @click="exit" align="center" :size="10" class="p-[8px_6px] rounded-4px hover:bg-[--tray-hover-e]">
        <span>退出</span>
      </n-flex>
    </n-flex>
  </n-flex>
</template>
<script setup lang="tsx">
import { useWindow } from '@/hooks/useWindow.ts'
import { invoke } from '@tauri-apps/api/tauri'
import { statusItem } from './home-window/onlineStatus/config.ts'
import { onlineStatus } from '@/stores/onlineStatus.ts'
import { appWindow } from '@tauri-apps/api/window'
import { listen } from '@tauri-apps/api/event'

const { checkWinExist, createWebviewWindow } = useWindow()
const OLStatusStore = onlineStatus()

const division = () => {
  return <div class={'h-1px bg-[--line-color] w-full'}></div>
}

const toggleStatus = (url: string, title: string) => {
  OLStatusStore.setOnlineStatus(url, title)
  appWindow.hide()
}

const exit = async () => {
  await invoke('exit').catch((error) => {
    console.error('退出失败:', error)
  })
}

onMounted(() => {
  // 暂停图标闪烁
  listen('stop', async () => {
    await invoke('tray_blink', { isRun: false }).catch((error) => {
      console.error('暂停闪烁失败:', error)
    })
  })
})
</script>

<style scoped lang="scss">
.tray {
  @apply bg-[--center-bg-color] size-full p-8px box-border select-none text-[--text-color] text-12px;
}
</style>