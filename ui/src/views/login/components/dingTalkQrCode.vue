<template>
  <div id="ding-talk-qr" class="ding-talk-qrName"></div>
</template>

<script lang="ts" setup>
import { useRouter } from 'vue-router'
import { useScriptTag } from '@vueuse/core'
import { defineProps, onMounted, ref } from 'vue'
import UserApi from '@/api/user'
import useStore from '@/stores'

// 声明 DTFrameLogin 和 QRLogin 的类型
declare global {
  interface Window {
    DTFrameLogin: (
      frameParams: IDTLoginFrameParams,
      loginParams: IDTLoginLoginParams,
      successCbk: (result: IDTLoginSuccess) => void,
      errorCbk?: (errorMsg: string) => void
    ) => void
    QRLogin: (QRLogin: qrLogin) => Record<any, any>
  }
}

// 定义接口类型
interface IDTLoginFrameParams {
  id: string
  width?: number
  height?: number
}

interface IDTLoginLoginParams {
  redirect_uri: string
  response_type: string
  client_id: string
  scope: string
  prompt: string
  state?: string
  org_type?: string
  corpId?: string
  exclusiveLogin?: string
  exclusiveCorpId?: string
}

interface IDTLoginSuccess {
  redirectUrl: string
  authCode: string
  state?: string
}

interface qrLogin {
  id: string
  goto: string
  width: string
  height: string
  style?: string
}

const props = defineProps<{
  config: {
    app_secret: string
    app_key: string
  }
}>()

const router = useRouter()
const { user } = useStore()
const { load } = useScriptTag('https://g.alicdn.com/dingding/h5-dingtalk-login/0.21.0/ddlogin.js')
const isConfigReady = ref(false)

const initActive = async () => {
  try {
    await load(true)
    if (!isConfigReady.value) {
      return
    }

    const data = {
      appKey: props.config.app_key,
      appSecret: props.config.app_secret
    }

    const redirectUri = encodeURIComponent(window.location.origin)
    window.DTFrameLogin(
      {
        id: 'ding-talk-qr',
        width: 300,
        height: 300
      },
      {
        redirect_uri: redirectUri,
        client_id: data.appKey || '',
        scope: 'openid',
        response_type: 'code',
        state: 'fit2cloud-ding-qr',
        prompt: 'consent'
      },
      (loginResult) => {
        const authCode = loginResult.authCode
        user.dingCallback(authCode).then(() => {
          router.push({ name: 'home' })
        })
      },
      (errorMsg: string) => {
        console.error(errorMsg)
      }
    )
  } catch (error) {
    console.error(error)
  }
}

onMounted(() => {
  // 模拟 config 加载完成
  setTimeout(() => {
    isConfigReady.value = true
    initActive()
  }, 1000)
})
</script>

<style lang="less" scoped>
.ding-talk-qrName {
  width: 300px;
  height: 300px;
}
</style>
