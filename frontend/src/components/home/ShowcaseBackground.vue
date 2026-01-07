<template>
  <!-- 渐变背景 -->
  <div class="showcase-background" :class="{ 'is-ready': isReady }">
    <div class="showcase-gradient"></div>
    <div class="showcase-overlay"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

/**
 * 渐变背景组件
 *
 * 功能：
 * - 提供平滑的渐变背景
 * - 毛玻璃遮罩效果
 * - 平滑淡入过渡
 */

// 是否准备好显示
const isReady = ref(false)

onMounted(() => {
  // 短暂延迟后显示，确保 DOM 渲染完成
  requestAnimationFrame(() => {
    requestAnimationFrame(() => {
      isReady.value = true
    })
  })
})
</script>

<style scoped>
/* 背景容器 */
.showcase-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  z-index: -1;
  overflow: hidden;
  opacity: 0;
  transition: opacity 0.6s ease-out;
}

.showcase-background.is-ready {
  opacity: 1;
}

/* 渐变背景 */
.showcase-gradient {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}

/* 毛玻璃遮罩层 */
.showcase-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(
    to bottom,
    rgba(255, 255, 255, 0.7) 0%,
    rgba(255, 255, 255, 0.65) 30%,
    rgba(255, 255, 255, 0.6) 100%
  );
  backdrop-filter: blur(2px);
}
</style>
