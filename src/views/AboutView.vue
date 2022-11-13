<template>
  <div class="about">
    <h1
      @touchstart.prevent="touchStart"
      @touchmove.prevent="touchMove"
      @touchend.prevent="touchEnd"
      @touchcancel.prevent="touchCancel"
      @click="handleClick"
    >
      long press
    </h1>
    <div>长按触发次数：{{ pressCount }}</div>
    <div>点击触发次数：{{ clickCount }}</div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const pressCount = ref(0);
const clickCount = ref(0);
const touchX = ref(0);
const touchY = ref(0);
const timeoutId = ref("");

function touchStart(e) {
  touchX.value = e.targetTouches[0].screenX;
  touchY.value = e.targetTouches[0].screenY;
  timeoutId.value && clearTimeout(timeoutId.value);
  timeoutId.value = setTimeout(() => {
    // 这里触发长按事件
    pressCount.value++;
    timeoutId.value = 0;
  }, 500);
}
function touchMove(e) {
  const moveX = e.targetTouches[0].screenX;
  const moveY = e.targetTouches[0].screenY;
  // Android 按住不动也会触发 toucheMove 事件
  if (touchX.value !== moveX || touchY.value !== moveY) {
    // 发生了移动
    timeoutId.value && clearTimeout(timeoutId.value);
    // 滑动不认为是点击事件
    timeoutId.value = 0;
  }
}
function touchEnd() {
  console.log("timeoutId", timeoutId.value);
  if (timeoutId.value) {
    // touch的事件少于500ms，认为是点击事件
    clearTimeout(timeoutId.value);
    // 执行点击事件
    clickCount.value++;
  }
}
function touchCancel() {
  // touch被中断
  timeoutId.value && clearTimeout(timeoutId.value);
}
function handleClick() {
  console.log("这里不会触发");
}
</script>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
