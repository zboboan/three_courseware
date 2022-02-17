<!-- 第三课 -->
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
    Fog,
    FogExp2,
  } from 'three';

  const puidu_webgl_output = ref();   // 整体容器

  const th = reactive({ 
    ctrl: new dat.GUI(),
    renderer:new WebGLRenderer(), // 渲染器对象
  });
  const scene = new Scene();  // 场景对象Scene
  // scene.fog = new Fog(0xff0000,1,100);
  // scene.fog = new FogExp2(0x0000ff,0.02);
  // scene.overrideMaterial = new MeshLambertMaterial({color:0x0000ff});
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

  // 地面网格
  let planeGeometry = new PlaneGeometry(100,100);
  let planeMaterial = new MeshLambertMaterial({color:0xAAAAAA});
  let plane = new Mesh(planeGeometry,planeMaterial);

  plane.rotation.x = -0.5 * Math.PI;
  plane.position.set(15,0,0);
  plane.receiveShadow = true;

  scene.add(plane);

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

  let geometry2 = new BoxGeometry(4,4,4);
  let material2 = new MeshLambertMaterial({color:0x00ff00});
  let cube2 = new Mesh(geometry2,material2);
  cube2.castShadow = true;

  cube2.position.x = -10;
  cube2.position.y = 10;
  cube2.position.z = 0;
  cube2.name = "cube2";
  scene.add(cube2);

  // console.log(scene.children);

  let findObj = scene.getObjectByName("cube2",false);
  console.log(findObj.position);

  light();

  let ctrlObj = {
    removeFindCube(){
      if(findObj instanceof Mesh){
        scene.remove(findObj);
      }
    },
    addNewCube(){
      let geometryTemp = new BoxGeometry(4,4,4);
      let materialTemp = new MeshLambertMaterial({color:0x0000ff});
      let cubeTemp = new Mesh(geometryTemp,materialTemp);
      cubeTemp.castShadow = true;

      cubeTemp.position.x = -Math.random() * 10;
      cubeTemp.position.y = Math.random() * 10;
      cubeTemp.position.z = Math.random() * 10;
      scene.add(cubeTemp);
    },
  };
  th.ctrl.add(ctrlObj,"removeFindCube");
  th.ctrl.add(ctrlObj,"addNewCube");

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

    // cube2.rotation.x += 0.01;
    // cube2.rotation.y += 0.01;
    // cube2.rotation.z += 0.01;

    // cube.rotation.x += ctrlObj.rotationSpeed;
    // cube.rotation.y += ctrlObj.rotationSpeed;
    // cube.rotation.z += ctrlObj.rotationSpeed;

    // gap.value += 0.01
    // gap.value += ctrlObj.jumpSpeed;
    // cube.position.x = 25 + (20 * (Math.sin(gap.value)));
    // cube.position.y = 6 + (20 * Math.abs(Math.cos(gap.value)));

    scene.traverse(obj=>{
      if (obj instanceof Mesh && obj !=plane){
        obj.rotation.x += 0.01;
        obj.rotation.y += 0.01;
        obj.rotation.z += 0.01;
      }
    })

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