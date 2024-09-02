<script setup>
// 引入three.js
import * as THREE from 'three';
// 引入扩展库OrbitControls.js
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
// 引入扩展库GLTFLoader.js
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

const width = 1920
const height = 1000

const scene = new THREE.Scene() // 创建三维场景
// 设置场景的背景色为白色
scene.background = new THREE.Color(0xffffff);


const geometry = new THREE.BoxGeometry(300, 300, 300) // 创建几何体
const material = new THREE.MeshLambertMaterial({
  color: 0xff0000,
  transparent: true,
}) // 创建材质


// 30:视场角度, width / height:Canvas画布宽高比, 1:近裁截面, 3000：远裁截面
const camera = new THREE.PerspectiveCamera(50, width / height, 600, 2800) // 实例化一个透视投影相机对象
camera.position.set(700, 700, 700) // 相机位置
camera.lookAt(0,0,0) // 相机观察目标

const mesh = new THREE.Mesh(geometry, material) // 几何体，材质 | 网格模型对象
mesh.position.set(150,150,1000) // 模型位置
mesh.castShadow = true; // 物体投射影子
mesh.receiveShadow = true 
scene.add(mesh) // 模型添加到场景中

const ambientLight = new THREE.AmbientLight(0xffffff, 0.5); // 环境光
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1.5); // 平行光
directionalLight.position.set(700, 700, 700);
directionalLight.castShadow = true; // 启用影子
directionalLight.shadow.mapSize.width = 600; // 影子纹理宽度
directionalLight.shadow.mapSize.height = 600; // 影子纹理高度
directionalLight.shadow.camera.left = -1600;
directionalLight.shadow.camera.right = 800;
directionalLight.shadow.camera.top = 800;
directionalLight.shadow.camera.bottom = -1000;
directionalLight.shadow.camera.near = 0.5;
directionalLight.shadow.camera.far = 2500;
scene.add(directionalLight)


// 可视化平行光阴影对应的正投影相机对象
const cameraHelper = new THREE.CameraHelper(directionalLight.shadow.camera);
scene.add(cameraHelper);

// DirectionalLightHelper：可视化平行光
const dirLightHelper = new THREE.DirectionalLightHelper(directionalLight, 5,0xff0000);
scene.add(dirLightHelper);

const axesHelper = new THREE.AxesHelper(3000) // 辅助观察坐标系
scene.add(axesHelper)


// 创建地板
const floorGeometry = new THREE.PlaneGeometry(10000, 100000, 10, 10); // 创建网格地板
const floorMaterial = new THREE.ShadowMaterial({ 
    color: 0xffffff, // 白色
    roughness: 0, // 光泽度，0 为光滑，1 为粗糙
    metalness: 0.5, // 金属感，0 为非金属，1 为金属
    clearcoat: 1, // 清漆层，0 为无清漆，1 为完全清漆
    clearcoatRoughness: 0.1, // 清漆粗糙度，
    opacity: 0.5
}); // 设置为白色
const floor = new THREE.Mesh(floorGeometry, floorMaterial);
floor.rotation.x = -Math.PI / 2; // 使地板水平
floor.position.x = 0; // 将地板放置在y=0处
floor.position.y = 0; // 将地板放置在y=0处
floor.position.z = 0; // 将地板放置在y=0处
floor.receiveShadow = true; 
scene.add(floor);

const lineMaterial = new THREE.LineBasicMaterial({ color: 11382189 }); // 线条颜色为黑色
const gridHelper = new THREE.GridHelper(10000, 80); // 创建网格辅助线
gridHelper.material = lineMaterial;
scene.add(gridHelper);


const readerer = new THREE.WebGLRenderer() // 创建渲染器对象
readerer.shadowMap.enabled = true
readerer.shadowMap.type = THREE.PCFSoftShadowMap;
readerer.setSize(width, height) // 设置canvas画布尺寸
document.getElementById('app').appendChild(readerer.domElement)

const loader = new GLTFLoader();
loader.load('/public/无标题.glb', function (gltf) {
    const model = gltf.scene;
    model.position.set(100, 100, 100); // 确保模型的位置在相机视野中
    model.scale.set(80, 80, 80);    // 调整模型的缩放
    model.traverse((child) => {
        if (child.isMesh) {
            child.castShadow = true; // 模型投射影子
        }
    });
    scene.add(model);
    // 如果有动画
}, undefined, function (error) {
    console.error(error);
});




const controls = new OrbitControls(camera, readerer.domElement);
controls.enableDamping = true; // 启用阻尼效果
controls.dampingFactor = 0.25; // 阻尼因子
controls.enableZoom = true; // 启用缩放


window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    readerer.setSize(window.innerWidth, window.innerHeight);
});




function animate() {
    requestAnimationFrame(animate);
    readerer.render(scene, camera);
}

animate()
</script>

<template>
</template>

<style scoped>
</style>
