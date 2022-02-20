<!-- 第九课 -->
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
    OrthographicCamera,
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
  // let camera = new PerspectiveCamera(75,window.innerWidth / window.innerHeight,0.1,1000); //透视相机
  let camera:any = new OrthographicCamera(window.innerWidth/-8,window.innerWidth/8,window.innerHeight/8,window.innerHeight/-8,50,100);
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
  for (let i=0;i<(planeGeometry.parameters.height/5);i++){
    for (let j=0;j<(planeGeometry.parameters.width/5);j++){
      let cubeGeo = new BoxGeometry(4,4,4);
      let cubeMaterial = new MeshLambertMaterial();
      cubeMaterial.color = new Color(0,Math.random() * 0.25 + 0.5,0);
      let cube = new Mesh(cubeGeo,cubeMaterial);

      cube.position.x = -(planeGeometry.parameters.width/2)+15+(i*5);
      cube.position.y = 2;
      cube.position.z = -(planeGeometry.parameters.height/2)+5+(j*5);

      scene.add(cube);
    }
  }

let ctrlObj = {
  showText:"透视投影摄像机",
  changeCamera(){
    if (camera instanceof PerspectiveCamera){
      camera = new OrthographicCamera(window.innerWidth/-16,window.innerWidth/16,window.innerHeight/16,window.innerHeight/-16,-200,500);
      camera.position.x = -30;
      camera.position.y = 40;
      camera.position.z = 30;
      camera.lookAt(scene.position);
      this.showText = "正交投影摄像机";
    }else{
      camera = new PerspectiveCamera(75,window.innerWidth / window.innerHeight,0.1,1000);
      camera.position.x = -30;
      camera.position.y = 40;
      camera.position.z = 30;
      camera.lookAt(scene.position);
      this.showText = "透视投影摄像机";
    }
  }
}

  th.ctrl.add(ctrlObj,"showText").listen();
  th.ctrl.add(ctrlObj,"changeCamera");

  let geometry = new BoxGeometry(8,8,8);
  let material = new MeshLambertMaterial({color:0xff2288});
  let cube = new Mesh(geometry,material);
  cube.position.x = 0;
  cube.position.y = 8;
  cube.position.z = 0;
  scene.add(cube);

  
  onMounted(()=>{
    puidu_webgl_output.value.appendChild(th.renderer.domElement);

    window.addEventListener('resize',()=>{
      camera.aspect = window.innerWidth/window.innerHeight;
      camera.updateProjectionMatrix();
      th.renderer.setSize(window.innerWidth,window.innerHeight);
    },false);
  });

  light();

  let pos = ref(0);

  // MARK: renderScene
  let renderScene=():void=>{
    // pos.value += 0.01;
    cube.position.x = 10 + (100 * (Math.sin(pos.value)));
    camera.lookAt(cube.position);
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