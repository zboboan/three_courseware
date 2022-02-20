<!-- 第七课 -->
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
    TorusGeometry,
    CylinderGeometry,
    BufferGeometry,
    BufferAttribute,
    MeshBasicMaterial,
    DoubleSide,
    WireframeGeometry,
    LineSegments,
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

  // 地面网格
  let planeGeometry = new PlaneGeometry(100,100);
  let planeMaterial = new MeshLambertMaterial({color:0xAAAAAA});
  let plane = new Mesh(planeGeometry,planeMaterial);

  plane.rotation.x = -0.5 * Math.PI;
  plane.position.set(15,0,0);
  plane.receiveShadow = true;

  scene.add(plane);

  // 创建几何体
  let geometry = new BoxGeometry(8,8,8);      //几何体
  let material = new MeshLambertMaterial({color:0xff2288});   //材质
  let cube = new Mesh(geometry,material);     //网格
  cube.castShadow = true;

  cube.position.x = 34;
  cube.position.y = 8;
  cube.position.z = 10;
  // scene.add(cube); 

  let refGeo = new BoxGeometry(8,8,8);      //几何体
  let refmaterial = new MeshLambertMaterial({color:0xff2288});   //材质
  let refcube = new Mesh(refGeo,refmaterial);     //网格
  refcube.castShadow = true;

  refcube.position.x = 0;
  refcube.position.y = 10;
  refcube.position.z = 0;
  scene.add(refcube);
  
  let showGeo = new BoxGeometry(8,8,8);      //几何体
  let showmaterial = new MeshLambertMaterial({color:0x00ff00});   //材质
  let showcube = new Mesh(showGeo,showmaterial);     //网格
  showcube.castShadow = true;

  showcube.position.x = 0;
  showcube.position.y = 10;
  showcube.position.z = 0;
  scene.add(showcube); 

  //圆环几何体
  let torusgeo = new TorusGeometry(10,2,10,10);
  let torusmaterial = new MeshLambertMaterial({color:0xff2288});
  let torus = new Mesh(torusgeo,torusmaterial);
  torus.castShadow = true;
  torus.position.x = 20;
  torus.position.y = 10;
  torus.position.z = -20;
  // scene.add(torus); 

  //圆柱几何体
  let cylindergeo = new CylinderGeometry(4,4,14);
  let cylindermaterial = new MeshLambertMaterial({color:0xff2288});
  let cylinder = new Mesh(cylindergeo,cylindermaterial);
  cylinder.castShadow = true;
  cylinder.position.x = -10;
  cylinder.position.y = 10;
  cylinder.position.z = -20;
  // scene.add(cylinder); 

  //自定义几何体
  let geometryCustom = new BufferGeometry();
  let vertices = new Float32Array([
      //第一个面
      0,0,0,
      10,0,0,
      0,10,0,
      10,0,0,
      0,10,0,
      10,10,0,
      //第二个面
      0,0,0,
      0,10,0,
      0,10,10,
      0,10,10,
      0,0,0,
      0,0,10,
      //第三个面
      10,0,0,
      10,10,0,
      10,10,10,
      10,0,0,
      10,0,10,
      10,10,10,
      //第四个面
      0,0,0,
      0,0,10,
      10,0,0,
      10,0,0,
      10,0,10,
      0,0,10,
      //第五个面
      0,10,0,
      0,10,10,
      10,10,0,
      10,10,0,
      10,10,10,
      0,10,10,
      //第六个面
      0,0,10,
      10,0,10,
      0,10,10,
      10,0,10,
      0,10,10,
      10,10,10,
      //第七个面
      10,10,0,
      0,10,0,
      2,20,5,
      //第八个面
      0,10,0,
      0,10,10,
      2,20,5,
      //第九个面
      0,10,10,
      10,10,10,
      2,20,5,
      //第十个面
      10,10,10,
      10,10,0,
      2,20,5,
  ])

  let attribute = new BufferAttribute(vertices,3);
  geometryCustom.attributes.position = attribute;
  let materialCustom = new MeshBasicMaterial({
      color:0x0000ff,
      side:DoubleSide,
  });

  let mesh = new Mesh(geometryCustom,materialCustom);
  // scene.add(mesh);

  console.log(geometryCustom);
  let wireFrame = new WireframeGeometry(geometryCustom);
  console.log(wireFrame);
  let line:any = new LineSegments(wireFrame);
  line.material.depthTest = false;
  // line.material.transparent = false;
  // line.material.opacity = 0.5;
  // scene.add(line);

  //克隆方法
  // th.ctrl.add({
  //   clone(){
  //     let cloneGeometry = mesh.geometry.clone();
  //     let clonematerial = new MeshBasicMaterial({
  //       color:0xff0000,
  //       side:DoubleSide,
  //     });

  //     let clonemesh = new Mesh(cloneGeometry,clonematerial);
  //     clonemesh.translateZ(20);
  //     clonemesh.name = "copy";
  //     scene.remove((scene as any).getObjectByName("copy"));
  //     scene.add(clonemesh);

  //     let clonewireFrame = new WireframeGeometry(cloneGeometry);
  //     let cloneline:any = new LineSegments(clonewireFrame);
  //     cloneline.material.depthTest = true;
  //     cloneline.translateZ(20);
  //     scene.add(cloneline);
  //   }
  // },"clone")

  let ctrlObj = {
    scaleX:1,
    scaleY:1,
    scaleZ:1,
    positionX:0,
    positionY:10,
    positionZ:0,
    rotationX:0,
    rotationY:0,
    rotationZ:0,
    visible:true,
    translateX:0,
    translateY:0,
    translateZ:0,
  };

  th.ctrl.add(ctrlObj,"scaleX",0,5);
  th.ctrl.add(ctrlObj,"scaleY",0,5);
  th.ctrl.add(ctrlObj,"scaleZ",0,5);

  th.ctrl.add(ctrlObj,"positionX",-30,30);
  th.ctrl.add(ctrlObj,"positionY",-30,30);
  th.ctrl.add(ctrlObj,"positionZ",-30,30);

  th.ctrl.add(ctrlObj,"rotationX",-5,5);
  th.ctrl.add(ctrlObj,"rotationY",-5,5);
  th.ctrl.add(ctrlObj,"rotationZ",-5,5);

  th.ctrl.add(ctrlObj,"visible");

  th.ctrl.add(ctrlObj,"translateX",-25,25);
  th.ctrl.add(ctrlObj,"translateY",-25,25);
  th.ctrl.add(ctrlObj,"translateZ",-25,25);

  onMounted(()=>{
    puidu_webgl_output.value.appendChild(th.renderer.domElement);

    window.addEventListener('resize',()=>{
      camera.aspect = window.innerWidth/window.innerHeight;
      camera.updateProjectionMatrix();
      th.renderer.setSize(window.innerWidth,window.innerHeight);
    },false);
  });

  light();

  let gap =ref(0);

  // MARK: renderScene
  let renderScene=():void=>{
    showcube.scale.set(ctrlObj.scaleX,ctrlObj.scaleY,ctrlObj.scaleZ);
    showcube.position.set(ctrlObj.positionX,ctrlObj.positionY,ctrlObj.positionZ);
    showcube.rotation.set(ctrlObj.rotationX,ctrlObj.rotationY,ctrlObj.rotationZ);
    showcube.visible = ctrlObj.visible;                
    showcube.translateX(ctrlObj.translateX);
    showcube.translateY(ctrlObj.translateY);
    showcube.translateZ(ctrlObj.translateZ);

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