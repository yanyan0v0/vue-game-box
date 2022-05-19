<template>
  <div class="shoot-container">
    <div class="shoot-header">
      <div class="shoot-header--left">倒计时： {{ time }}</div>
      <div class="shoot-header--right">分数： {{ score }}</div>
    </div>
    <div class="shoot-content">
      <div
        class="shoot-content__point"
        :style="{ left: `${point.x}px`, top: `${point.y}px` }"
        @click="clickHandler"
      ></div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { onMounted, reactive, ref } from "vue";
const time = ref(120);
const score = ref(0);
const point = reactive({ x: 100, y: 100 });
const contentSize = reactive({ width: 100, height: 100 });
const clickHandler = () => {
  // 随机位置
  const x = ~~(Math.random() * (contentSize.width - 10) + 5);
  const y = ~~(Math.random() * (contentSize.height - 10) + 5);
  point.x = x;
  point.y = y;
  console.log("point:", point);

  score.value += 1;
};

onMounted(() => {
  // 获取.shoot-content宽高大小
  const contentBody = document.querySelector(".shoot-content") as HTMLElement;
  const content = contentBody.getBoundingClientRect();
  contentSize.height = content.height;
  contentSize.width = content.width;
  console.log("contentSize:", contentSize);

  // 记录当前鼠标位置
  const mousePoint = { x: 0, y: 0 };
  // 监听鼠标移动事件
  const onMouseMove = function (e: MouseEvent) {
    if (mousePoint.x === 0 && mousePoint.y === 0) {
      mousePoint.x = e.offsetX;
      mousePoint.y = e.offsetY;
      return;
    }
    const moveX = e.offsetX - mousePoint.x;
    if (moveX < -5 || moveX > 5) {
      mousePoint.x = e.offsetX;
      mousePoint.y = e.offsetY;
      return;
    }
    const moveY = e.offsetY - mousePoint.y;
    if (moveY < -5 || moveY > 5) {
      mousePoint.x = e.offsetX;
      mousePoint.y = e.offsetY;
      return;
    }
    point.x -= e.offsetX - mousePoint.x;
    point.y -= e.offsetY - mousePoint.y;

    mousePoint.x = e.offsetX;
    mousePoint.y = e.offsetY;
  };
  contentBody.addEventListener("mousemove", onMouseMove);
});

setInterval(() => {
  time.value -= 1;
}, 1000);
</script>

<style lang="less" scoped>
.shoot {
  &-container {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
  }
  &-header {
    display: flex;
    justify-content: space-between;
    height: 40px;
  }
  &-content {
    position: relative;
    flex: 1;
    // cursor: none;
    &__point {
      position: absolute;
      left: 0;
      right: 0;
      width: 10px;
      height: 10px;
      border-radius: 50%;
      border: 1px solid #ccc;
      background-color: red;
    }
  }
}
</style>
