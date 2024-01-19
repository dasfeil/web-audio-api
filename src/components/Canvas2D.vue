<template>
  <canvas ref="canvas"> </canvas>
</template>

<script setup lang="ts">
import * as THREE from "three";
import { onMounted, ref } from "vue";
const canvas = ref<HTMLCanvasElement | null>(null);
const scene = new THREE.Scene();
scene.background = new THREE.Color("white");
const camera = new THREE.OrthographicCamera();

const geometry = new THREE.BoxGeometry(0.25, 0.25, 1);
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);
camera.position.z = 5;
cube.position.x = 0
cube.position.y = 0

onMounted(() => {
  const renderer = new THREE.WebGLRenderer({ canvas: canvas.value! });
  renderer.setSize(640, 640);
  function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
  }
  animate();
});
</script>
