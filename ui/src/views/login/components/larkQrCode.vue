<template>
  <div id="lark-qr" class="lark-qrName"></div>
</template>

<script lang="ts" setup>
import { useScriptTag } from '@vueuse/core'
import { defineProps, onMounted } from 'vue'

const { load } = useScriptTag(
  'https://lf-package-cn.feishucdn.com/obj/feishu-static/lark/passport/qrcode/LarkSSOSDKWebQRCode-1.0.3.js'
)

const props = defineProps<{
  config: {
    app_secret: string
    app_key: string
  }
}>()

const initActive = async () => {
  const scriptLoaded = await load(true)
  if (!scriptLoaded) {
    console.error('飞书二维码 SDK 加载失败')
    return
  }

  const data = {
    agentId: props.config.app_key,
    appSecret: props.config.app_secret
  }

  const redirectUrl = encodeURIComponent(`${window.location.origin}/api/feishu`)
  const url = `https://passport.feishu.cn/suite/passport/oauth/authorize?client_id=${data.agentId}&redirect_uri=${redirectUrl}&response_type=code&state=fit2cloud-lark-qr`

  const QRLoginObj = window.QRLogin({
    id: 'lark-qr',
    goto: url,
    width: '300',
    height: '300',
    style: 'width:300px;height:300px'
  })

  window.addEventListener('message', async (event: any) => {
    if (QRLoginObj.matchOrigin(event.origin) && QRLoginObj.matchData(event.data)) {
      const loginTmpCode = event.data.tmp_code
      window.location.href = `${url}&tmp_code=${loginTmpCode}`
    }
  })
}

onMounted(() => {
  initActive()
})
</script>

<style lang="less" scoped>
.lark-qrName {
  width: 300px;
  height: 300px;
}
</style>
