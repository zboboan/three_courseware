<!-- 第五课 -->
<template>
  <!-- 我们将把 threejs 渲染效果显示在这个div  -->
  <div ref="puidu_webgl_output" ></div> 
</template>

<script setup lang="ts">
  // stats 插件没有TS版 所以这里就不用了
  import {ref,reactive,onMounted,onUnmounted} from 'vue';
  import * as dat from 'dat.gui';
  import { 
    AmbientLight,
    AxesHelper,
    BoxGeometry,
    Color,
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
    ctrl: new dat.GUI(),
    renderer:new WebGLRenderer(), // 渲染器对象
  });
  const scene = new Scene();  // 场景对象Scene
  // 渲染 场景
  th.renderer.setClearColor(new Color(0x000000));
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

  // 创建几何体
  let geometry = new BoxGeometry(8,8,8);
  let material = new MeshLambertMaterial({color:0xff2288});
  let cube = new Mesh(geometry,material);
  cube.castShadow = true;
  cube.rotation.x += 0.80;
  cube.rotation.y += 0.80;
  

  cube.position.x = 4;
  cube.position.y = 8;
  cube.position.z = 10;
  scene.add(cube);

  planeGeometryMain();
  light();

  let ctrlObj = {
    rotationSpeed:0.01,
    jumpSpeed:0.01,
  };
  th.ctrl.add(ctrlObj,"rotationSpeed",0,1);
  th.ctrl.add(ctrlObj,"jumpSpeed",0,1);

  onMounted(()=>{
    puidu_webgl_output.value.appendChild(th.renderer.domElement);

    window.addEventListener('resize',()=>{
      camera.aspect = window.innerWidth/window.innerHeight;
      camera.updateProjectionMatrix();
      th.renderer.setSize(window.innerWidth,window.innerHeight);
    },false);
  });

  let gap =ref(0);

  // MARK: renderScene
  let renderScene=():void=>{
    // cube.rotation.x += 0.01;
    // cube.rotation.y += 0.01;
    // cube.rotation.z += 0.01;

    cube.rotation.x += ctrlObj.rotationSpeed;
    cube.rotation.y += ctrlObj.rotationSpeed;
    cube.rotation.z += ctrlObj.rotationSpeed;

    // gap.value += 0.01
    gap.value += ctrlObj.jumpSpeed;
    cube.position.x = 25 + (20 * (Math.sin(gap.value)));
    cube.position.y = 6 + (20 * Math.abs(Math.cos(gap.value)));

    requestAnimationFrame(renderScene);
    th.renderer.render(scene,camera);
  }
  renderScene()

  onUnmounted(()=>{
    th.ctrl.destroy() 
  });

</script>

<style scoped lang="less">

</style>