<!-- 第三课 -->
<template>
  <!-- 我们将把 threejs 渲染效果显示在这个div  -->
  <div ref="puidu_webgl_output" ></div> 
</template>

<script setup lang="ts">
  import {ref,reactive,onMounted,onUnmounted} from 'vue';
  import * as dat from 'dat.gui';
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
  import { 
    AmbientLight,
    AxesHelper,
    BoxGeometry,
    Color,
    Mesh,
    MeshLambertMaterial,
    OrthographicCamera,
    PerspectiveCamera,
    PlaneGeometry,
    Scene, 
    SpotLight, 
    TextureLoader, 
    Vector2, 
    WebGLRenderer,
  } from 'three';

  const puidu_webgl_output = ref();   // 整体容器

  const th = reactive({ 
    ctrl: new dat.GUI(),
    renderer:new WebGLRenderer(), // 渲染器对象
    ambienLight : new AmbientLight(0xcccccc), // 自然光
    planeGeometry:new PlaneGeometry(100,100),// 地面
    spotLight:new SpotLight(0xFFFFFF),// 聚光灯
  });

  const scene = new Scene();  // 场景对象Scene
  const axes = new AxesHelper(50); // 轴长度
  scene.add(axes); // 坐标轴长度

  // 渲染 场景
  th.renderer.shadowMap.enabled = true;
  th.renderer.setSize(window.innerWidth,window.innerHeight);

  // 影相机
  let camera = new PerspectiveCamera(75,window.innerWidth/window.innerHeight,5,10000); //透视相机
  camera.position.set(-30, 40, 30);
  camera.lookAt(scene.position);


  let lamplight = ()=>{
    th.spotLight.position.set(-60, 40, -65);
    th.spotLight.castShadow = true;
    th.spotLight.shadow.mapSize = new Vector2(1024, 1024);
    th.spotLight.shadow.camera.far = 130;
    th.spotLight.shadow.camera.near = 40;
    scene.add(th.spotLight);

    scene.add(th.ambienLight); // 自然光
  }

  let ground=()=>{
    // 地面
    const lambertMaterial = new MeshLambertMaterial({//材质对象Lambert
      color:0xcccccc
    });
    const plane = new Mesh(th.planeGeometry,lambertMaterial); // 网格模型
    plane.rotation.x=-0.5*Math.PI;
    plane.position.set(15, 0, 0);
    plane.receiveShadow = true;
    scene.add(plane);
  }

  // MARK: OrbitControls 轨道控制器
  let controls = new OrbitControls(camera,th.renderer.domElement);
  controls.update();


  let boxGeometry=()=>{
    let geometry = new BoxGeometry(8,8,8);
    let material = new MeshLambertMaterial({color:0xff2288});
    let cube = new Mesh(geometry,material);

    cube.position.x=0;
    cube.position.y=8;
    cube.position.z=0;
    scene.add(cube);
  }


  onMounted(()=>{
    
    puidu_webgl_output.value.appendChild(th.renderer.domElement);
    
    window.addEventListener('resize',()=>{
      camera.aspect = window.innerWidth/window.innerHeight;
      camera.updateProjectionMatrix();
      th.renderer.setSize(window.innerWidth,window.innerHeight);
    },false);

    lamplight()
    ground()
    boxGeometry()
  });



  // MARK: renderScene
  let renderScene=():void=>{
    requestAnimationFrame(renderScene);
    th.renderer.render(scene,camera);
  }
  renderScene()

  onUnmounted(()=>{
    th.ctrl.destroy()  // 销毁dat.GUI
  });






</script>

<style scoped lang="less">

</style>