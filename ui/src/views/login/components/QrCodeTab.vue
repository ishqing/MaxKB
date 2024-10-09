<template>
  <div class="login-qrcode">
    <div class="tabs">
      <button
        v-for="item in tabs"
        :key="item.key"
        @click="selectTab(item.key)"
        :class="{ active: activeKey === item.key }"
      >
        {{ item.value }}
      </button>
    </div>

    <div class="qrcode">
      <div v-if="activeKey === 'dingtalk'" class="login-qrcode">
        <div class="qrcode">
          <div class="title">
            <img :src="logoUrl" alt="" class="icon" />
            钉钉登录
          </div>
          <ding-talk-qr-code :config="config" />
        </div>
      </div>
      <div v-if="activeKey === 'lark'" class="login-qrcode">
        <div class="qrcode">
          <div class="title">
            <img :src="logoUrl" alt="" class="icon" />
            飞书登录
          </div>
          <lark-qr-code :config="config" />
        </div>
      </div>
      <div v-if="activeKey === 'wecom'" class="login-qrcode">
        <div class="qrcode">
          <we-com-qr-code :config="config" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, onMounted, ref } from 'vue'
import dingTalkQrCode from './dingTalkQrCode.vue'
import larkQrCode from './larkQrCode.vue'
import weComQrCode from './weComQrCode.vue'
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
const logoUrl = ref('')

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
    const logoMap: { [key: string]: string } = {
      wecom: 'wechat-work',
      dingtalk: 'dingtalk',
      lark: 'lark'
    }
    logoUrl.value = new URL(`../../../assets/logo_${logoMap[key] || ''}.svg`, import.meta.url).href
  }
}

const selectTab = (key: string) => {
  activeKey.value = key
  updateConfig(key)
}
</script>

<style scoped lang="scss">
.tabs button {
  padding: 10px 20px;
  cursor: pointer;
  border: none;
  outline: none;
  background-color: #f1f1f1;
  margin-right: 5px;
  &.active {
    background-color: #ddd;
  }
}
.login-qrcode {
  display: flex;
  align-items: center;
  margin-top: 24px;
  min-width: 480px;
  flex-direction: column;
  .qrcode {
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
    border-radius: 8px;
    background: #ffffff;
    flex-direction: column;
  }
  .title {
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden;
    font-size: 18px;
    font-weight: 500;
    line-height: 26px;
    margin-bottom: -24px;
    z-index: 100000;
    .ed-icon {
      margin-right: 8px;
      font-size: 24px;
    }
  }
}
</style>
