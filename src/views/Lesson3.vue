<!-- 第三课 -->
<template>
  <!-- 我们将把 threejs 渲染效果显示在这个div  -->
  <div ref="puidu_webgl_output" ></div> 
</template>

<script setup lang="ts">
  import {ref,reactive,onMounted} from 'vue';
  import { 
    BoxGeometry,
    Mesh,
    MeshBasicMaterial,
    PerspectiveCamera,
    Scene, 
    WebGLRenderer,
  } from 'three';

  const puidu_webgl_output = ref();   // 整体容器

  const th = reactive({ 
    renderer:new WebGLRenderer(), // 渲染器对象
  });
  const scene = new Scene();  // 场景对象Scene
  // 渲染 场景
  th.renderer.setSize(window.innerWidth,window.innerHeight);

  // 影相机
  let camera = new PerspectiveCamera(75,window.innerWidth / window.innerHeight,0.1,1000); //透视相机
  camera.position.z = 5;

  let boxGeometry=()=>{
    var geometry = new BoxGeometry();
    var material = new MeshBasicMaterial({color:0xff2288});
    var cube = new Mesh(geometry,material);
    cube.rotation.x += 0.80;
    cube.rotation.y += 0.80;
    scene.add(cube);
  }

  boxGeometry()

  onMounted(()=>{
    puidu_webgl_output.value.appendChild(th.renderer.domElement);
  });

  th.renderer.render(scene,camera);

</script>

<style scoped lang="less">

</style>