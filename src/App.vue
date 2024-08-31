<script setup>
// 引入three.js
import * as THREE from 'three';
// 引入扩展库OrbitControls.js
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
// 引入扩展库GLTFLoader.js
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';


const width = 800
const height = 500

const scene = new THREE.Scene() // 创建三维场景
// 设置场景的背景色为白色
scene.background = new THREE.Color(0xffffff);


const geometry = new THREE.BoxGeometry(300, 300, 300) // 创建几何体

const material = new THREE.MeshStandardMaterial({
  color: 0xff0000,
  transparent: true,
}) // 创建材质


// 30:视场角度, width / height:Canvas画布宽高比, 1:近裁截面, 3000：远裁截面
const camera = new THREE.PerspectiveCamera(50, width / height, 600, 1800) // 实例化一个透视投影相机对象
camera.position.set(1000, 1000, 1000) // 相机位置
camera.lookAt(0,0,0) // 相机观察目标


const mesh = new THREE.Mesh(geometry, material) // 几何体，材质 | 网格模型对象
mesh.position.set(300,300,300) // 模型位置


scene.add(mesh) // 模型添加到场景中


const pointLight = new THREE.PointLight(0xffffff, 1.0);
pointLight.position.set(500, 1000, 1000); 
scene.add(pointLight)

const axesHelper = new THREE.AxesHelper(2000) // 辅助观察坐标系
scene.add(axesHelper)



// 创建一个平面几何体作为地板
const planeGeometry = new THREE.PlaneGeometry(5000, 5000); // 调整尺寸适合你的场景
const planeMaterial = new THREE.MeshStandardMaterial({ color: 0xffffff }); // 白色材质

const plane = new THREE.Mesh(planeGeometry, planeMaterial);
plane.rotation.x = -Math.PI / 2; // 使平面水平放置
plane.position.y = 0; // 将地板放置在y=0处
plane.receiveShadow = true; // 允许接收阴影（可选）

scene.add(plane); // 将地板添加到场景中


const readerer = new THREE.WebGLRenderer() // 创建渲染器对象
readerer.shadowMap.enabled = true
readerer.shadowMap.type = THREE.PCFSoftShadowMap;

readerer.setSize(width, height) // 设置canvas画布尺寸
// readerer.render(scene, camera) // 执行渲染操作
document.getElementById('app').appendChild(readerer.domElement)


function animate() {
    requestAnimationFrame(animate);
    readerer.render(scene, camera);
}

animate()
</script>

<template>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
