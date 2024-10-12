<template>
  <el-tabs v-model="activeKey" @tab-click="selectTab">
    <template v-for="item in tabs" :key="item.key">
      <el-tab-pane :label="item.value" :name="item.key">
        <div class="text-center mt-16">
          <component
            :is="defineAsyncComponent(() => import(`./${item.key}QrCode.vue`))"
            :config="config"
          />
        </div>
      </el-tab-pane>
    </template>
  </el-tabs>
</template>

<script setup lang="ts">
import { defineProps, onMounted, ref, defineAsyncComponent } from 'vue'

import platformApi from '@/api/platform-source'

interface Tab {
  key: string
  value: string
}

interface PlatformConfig {
  app_key: string
  app_secret: string
  platform: string
  config: any
}

interface Config {
  app_key: string
  app_secret: string
  corpId?: string
  agentId?: string
}

const props = defineProps<{ tabs: Tab[] }>()
const activeKey = ref('dingtalk')
const allConfigs = ref<PlatformConfig[]>([])
const config = ref<Config>({ app_key: '', app_secret: '' })
// const logoUrl = ref('')

async function getPlatformInfo() {
  try {
    const res = await platformApi.getPlatformInfo()
    return res.data
  } catch (error) {
    return []
  }
}

onMounted(async () => {
  allConfigs.value = await getPlatformInfo()
  updateConfig(activeKey.value)
})

const updateConfig = (key: string) => {
  const selectedConfig = allConfigs.value.find((item) => item.platform === key)
  if (selectedConfig && selectedConfig.config) {
    config.value = selectedConfig.config
    //   const logoMap: { [key: string]: string } = {
    //     wecom: 'wechat-work',
    //     dingtalk: 'dingtalk',
    //     lark: 'lark'
    //   }
    //   logoUrl.value = new URL(`../../../assets/logo_${logoMap[key] || ''}.svg`, import.meta.url).href
  }
}

const selectTab = (key: string) => {
  activeKey.value = key
  updateConfig(key)
}
</script>

<style scoped lang="scss"></style>
