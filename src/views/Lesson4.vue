<!-- 第四课 -->
<template>
  <!-- 我们将把 threejs 渲染效果显示在这个div  -->
  <div ref="puidu_webgl_output" ></div> 
</template>

<script setup lang="ts">
  import {ref,reactive,onMounted} from 'vue';
  import { 
    AmbientLight,
    AxesHelper,
    BoxGeometry,
    Mesh,
    MeshLambertMaterial,
    PerspectiveCamera,
    PlaneGeometry,
    Scene, 
    SpotLight, 
    Vector2, 
    WebGLRenderer,
  } from 'three';

  const puidu_webgl_output = ref();   // 整体容器

  const th = reactive({ 
    renderer:new WebGLRenderer(), // 渲染器对象
  });
  const scene = new Scene();  // 场景对象Scene
  // 渲染 场景
  th.renderer.setSize(window.innerWidth,window.innerHeight);
  th.renderer.shadowMap.enabled = true;

  const axes = new AxesHelper(50); // 轴长度
  scene.add(axes); // 坐标轴长度
  
  // 影相机
  let camera = new PerspectiveCamera(75,window.innerWidth / window.innerHeight,0.1,1000); //透视相机
  camera.position.x = -30;
  camera.position.y = 45;
  camera.position.z = 35;
  camera.lookAt(scene.position);

  let light=()=>{
    // 光源
    let spotLight = new SpotLight(0xFFFFFF);
    spotLight.position.set(-60, 40, -65);
    spotLight.castShadow = true;
    spotLight.shadow.mapSize = new Vector2(1024, 1024);
    spotLight.shadow.camera.far = 130;
    spotLight.shadow.camera.near = 40;

    // If you want a more detailled shadow you can increase the 
    // mapSize used to draw the shadows.
    // spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
    scene.add(spotLight);

    let ambienLight = new AmbientLight(0xcccccc);
    scene.add(ambienLight);
  }

  let planeGeometryMain=()=>{
    // 地面网格
    let planeGeometry = new PlaneGeometry(100,100);
    let planeMaterial = new MeshLambertMaterial({color:0xAAAAAA});
    let plane = new Mesh(planeGeometry,planeMaterial);

    plane.rotation.x = -0.5 * Math.PI;
    plane.position.set(15,0,0);
    plane.receiveShadow = true;

    scene.add(plane);
  }

  let boxGeometry=()=>{
    // 创建几何体
    let geometry = new BoxGeometry(8,8,8);
    let material = new MeshLambertMaterial({color:0xff2288});
    let cube = new Mesh(geometry,material);
    cube.castShadow = true;
    // cube.rotation.x += 0.80;
    // cube.rotation.y += 0.80;
    

    cube.position.x = 4;
    cube.position.y = 10;
    cube.position.z = 20;
    scene.add(cube);
  }

  boxGeometry();
  planeGeometryMain();
  light();

  onMounted(()=>{
    puidu_webgl_output.value.appendChild(th.renderer.domElement);
  });

  th.renderer.render(scene,camera);

</script>

<style scoped lang="less">

</style>