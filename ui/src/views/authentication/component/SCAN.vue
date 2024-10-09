<template>
  <div v-loading="loading">
    <el-card shadow="hover" class="border-none cursor" style="--el-card-padding: 16px 24px">
      <div class="flex-between">
        <div class="flex align-center ml-8 mr-8">
          <!-- <img :src="item.logoSrc" alt="" class="icon" /> -->
          <h5>企业微信</h5>
          <el-tag type="success" class="ml-4"> 有效</el-tag>
        </div>
        <div>
          <el-button type="primary">接入</el-button>
          <span>
            <span class="mr-4">已开启</span>
            <el-switch size="small" v-model="isActive" />
          </span>
        </div>
      </div>
      <el-collapse-transition>
        <div v-if="isActive" class="border-t mt-16">
          <el-row :gutter="12" class="mt-16">
            <el-col :span="12">
              <el-text type="info">APP Key</el-text>

              <div class="mt-4 mb-16 flex align-center">
                <span class="vertical-middle lighter break-all"> xxxx </span>

                <el-button type="primary" text @click="copyClick(shareUrl)">
                  <AppIcon iconName="app-copy"></AppIcon>
                </el-button>
              </div>
            </el-col>
            <el-col :span="12">
              <el-text type="info">APP Secret</el-text>

              <div class="mt-4 mb-16 flex align-center">
                <span class="vertical-middle lighter break-all"> xxxx </span>

                <el-button type="primary" text @click="copyClick(shareUrl)">
                  <AppIcon iconName="app-copy"></AppIcon>
                </el-button>
                <span>
                  <el-button type="primary" text>
                    <el-icon><Hide /></el-icon>
                  </el-button>
                </span>
              </div>
            </el-col>
            <el-col :span="12">
              <el-text type="info">回调域名</el-text>

              <div class="mt-4 mb-16 flex align-center">
                <span class="vertical-middle lighter break-all"> xxxx </span>
              </div>
            </el-col>
          </el-row>
          <el-button type="primary"> 编辑 </el-button>
          <el-button> 校验 </el-button>
        </div>
      </el-collapse-transition>
    </el-card>
  </div>
</template>
<script setup lang="ts">
import { reactive, ref, watch, onMounted } from 'vue'
import { copyClick } from '@/utils/clipboard'

const loading = ref(false)
const isActive = ref(false)

onMounted(() => {})
</script>
<style lang="scss" scoped></style>
