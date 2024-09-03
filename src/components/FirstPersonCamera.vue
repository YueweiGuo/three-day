<template>
</template>

<script setup name="FirstPersonCamera">
import * as THREE from 'three';
import { PointerLockControls } from 'three/examples/jsm/controls/PointerLockControls.js';

const container = document.getElementById('app')

const scene = new THREE.Scene()
scene.fog = new THREE.FogExp2( 0xcccccc, 0.002 );


const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(0, 1.6, 5); // 相机位置

let yaw = 0;
let pitch = 0;

document.addEventListener('mousemove', (event) => {
    yaw -= event.movementX * 0.002;
    pitch -= event.movementY * 0.002;
    pitch = Math.max(-Math.PI / 2, Math.min(Math.PI / 2, pitch)); // clamp the pitch
});

function updateCameraRotation() {
    camera.rotation.x = pitch;
    camera.rotation.y = yaw;
}

let moveForward = false;
let moveBackward = false;
let moveLeft = false;
let moveRight = false;

document.addEventListener('keydown', (event) => {
  console.log(event.code)
    switch (event.code) {
        case 'ArrowUp':
        case 'KeyW':
            moveForward = true;
            break;
        case 'ArrowLeft':
        case 'KeyA':
            moveLeft = true;
            break;
        case 'ArrowDown':
        case 'KeyS':
            moveBackward = true;
            break;
        case 'ArrowRight':
        case 'KeyD':
            moveRight = true;
            break;
    }
});

document.addEventListener('keyup', (event) => {
    switch (event.code) {
        case 'ArrowUp':
        case 'KeyW':
            moveForward = false;
            break;
        case 'ArrowLeft':
        case 'KeyA':
            moveLeft = false;
            break;
        case 'ArrowDown':
        case 'KeyS':
            moveBackward = false;
            break;
        case 'ArrowRight':
        case 'KeyD':
            moveRight = false;
            break;
    }
});


function updateCameraPosition() {
    const speed = 0.1;
    if (moveForward) camera.position.z -= Math.cos(yaw) * speed;console.log(1)
    if (moveBackward) camera.position.z += Math.cos(yaw) * speed;console.log(2)
    if (moveLeft) camera.position.x -= Math.sin(yaw) * speed;console.log(3)
    if (moveRight) camera.position.x += Math.sin(yaw) * speed;console.log(4)
}

// 创建渲染器
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// 创建光源
const light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(5, 10, 7.5);
scene.add(light);

const ambientLight = new THREE.AmbientLight(0xffffff, 0.5); // 环境光
scene.add(ambientLight);

// 创建地板
const floorGeometry = new THREE.PlaneGeometry(100, 100);
const floorMaterial = new THREE.MeshLambertMaterial({ color: 0x808080 });
const floor = new THREE.Mesh(floorGeometry, floorMaterial);
floor.rotation.x = - Math.PI / 2;
scene.add(floor);


function createCharacter(x, z) {
  const characterGeometry = new THREE.BoxGeometry(1, 2, 1); // 简单的盒子代表角色
  const characterMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 });
  const character = new THREE.Mesh(characterGeometry, characterMaterial);
  character.position.set(x, 1, z);
  scene.add(character);
}

createCharacter(5, 5);
createCharacter(-5, 5);
createCharacter(5, -5);
createCharacter(-5, -5);
createCharacter(-36, -15);
createCharacter(-5, -50);
createCharacter(-20, -5);


// 初始化 PointerLockControls
const controls = new PointerLockControls(camera, renderer.domElement);
controls.movementSpeed = 5;
controls.lookSpeed = 0.1;

// 限制角度范围
controls.constrainVertical = true; // 启用垂直限制
controls.verticalMin = -Math.PI / 4; // 最小俯仰角
controls.verticalMax = Math.PI / 4;  // 最大俯仰角


// 控制相机移动
const moveSpeed = 5;
const keys = {};

document.addEventListener('keydown', (event) => {
  keys[event.code] = true;
});

document.addEventListener('keyup', (event) => {
  keys[event.code] = false;
});

function handleMovement() {
  const velocity = new THREE.Vector3();
  const direction = new THREE.Vector3();

  // Update velocity based on keys
  if (keys['KeyW']) direction.z -= 1;
  if (keys['KeyS']) direction.z += 1;
  if (keys['KeyA']) direction.x -= 1;
  if (keys['KeyD']) direction.x += 1;

  direction.normalize(); // Ensure consistent speed regardless of direction
  velocity.add(direction.multiplyScalar(moveSpeed * 0.1)); // Apply speed

  camera.position.add(velocity);
}

// 创建哈士奇模型（简化版）
function createHusky() {
  const husky = new THREE.Group();

  // 身体
  const bodyGeometry = new THREE.BoxGeometry(3, 1.5, 1);
  const bodyMaterial = new THREE.MeshBasicMaterial({ color: 0x808080 });
  const body = new THREE.Mesh(bodyGeometry, bodyMaterial);
  body.position.set(0, 0.75, 0);
  husky.add(body);

  // 头部
  const headGeometry = new THREE.BoxGeometry(1.5, 1.5, 1.5);
  const headMaterial = new THREE.MeshBasicMaterial({ color: 0x808080 });
  const head = new THREE.Mesh(headGeometry, headMaterial);
  head.position.set(0, 1.75, 0.75);
  husky.add(head);

  // 眼睛
  const eyeGeometry = new THREE.SphereGeometry(0.1, 16, 16);
  const eyeMaterial = new THREE.MeshBasicMaterial({ color: 0x000000 });
  const leftEye = new THREE.Mesh(eyeGeometry, eyeMaterial);
  leftEye.position.set(-0.3, 1.9, 1.2);
  husky.add(leftEye);

  const rightEye = new THREE.Mesh(eyeGeometry, eyeMaterial);
  rightEye.position.set(0.3, 1.9, 1.2);
  husky.add(rightEye);

  // 耳朵
  const earGeometry = new THREE.ConeGeometry(0.5, 1, 4);
  const earMaterial = new THREE.MeshBasicMaterial({ color: 0x808080 });
  const leftEar = new THREE.Mesh(earGeometry, earMaterial);
  leftEar.rotation.y = Math.PI;
  leftEar.position.set(-0.75, 2.5, 0);
  husky.add(leftEar);

  const rightEar = new THREE.Mesh(earGeometry, earMaterial);
  rightEar.rotation.y = Math.PI;
  rightEar.position.set(0.75, 2.5, 0);
  husky.add(rightEar);

  // 四肢
  const legGeometry = new THREE.CylinderGeometry(0.2, 0.2, 1, 8);
  const legMaterial = new THREE.MeshBasicMaterial({ color: 0x808080 });
  const frontLeftLeg = new THREE.Mesh(legGeometry, legMaterial);
  frontLeftLeg.position.set(-1, 0.25, -0.5);
  husky.add(frontLeftLeg);

  const frontRightLeg = new THREE.Mesh(legGeometry, legMaterial);
  frontRightLeg.position.set(1, 0.25, -0.5);
  husky.add(frontRightLeg);

  const backLeftLeg = new THREE.Mesh(legGeometry, legMaterial);
  backLeftLeg.position.set(-1, 0.25, 0.5);
  husky.add(backLeftLeg);

  const backRightLeg = new THREE.Mesh(legGeometry, legMaterial);
  backRightLeg.position.set(1, 0.25, 0.5);
  husky.add(backRightLeg);

  return husky;
}

const husky = createHusky();
scene.add(husky);


const animate = () => {
    requestAnimationFrame(animate);
    handleMovement();
    updateCameraRotation()
    // controls.update(); // 更新控制器
    renderer.render(scene, camera); // 渲染场景
}
animate();
</script>
