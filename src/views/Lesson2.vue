<!-- 第二课 -->
<template>
  <!-- 我们将把 threejs 渲染效果显示在这个div  -->
  <div ref="puidu_webgl_output" ></div> 
</template>

<script setup lang="ts">
  import {ref,reactive,onMounted,onUnmounted} from 'vue';
  import {REVISION} from 'three';
  const puidu_webgl_output = ref();   // 整体容器
  console.log("当前使用的three.js版本号==>",REVISION);
</script>

<style scoped lang="less">

</style>
