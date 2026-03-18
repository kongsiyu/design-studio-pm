<script setup lang="ts">
import { ref } from 'vue'

const msg = ref('Hello uni-app!')
</script>

<template>
  <view class="container">
    <view class="content">
      <text class="title">{{ msg }}</text>
      <text class="subtitle">项目管理小程序已就绪</text>
    </view>
  </view>
</template>

<style lang="scss" scoped>
.container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #f5f5f5;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16rpx;
}

.title {
  font-size: 48rpx;
  font-weight: bold;
  color: #1677FF;
}

.subtitle {
  font-size: 28rpx;
  color: #666666;
}
</style>
