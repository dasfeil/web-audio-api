<template>
  <canvas ref="canvas" class="m-auto" width="640" height="640"> </canvas>
</template>

<script setup lang="ts">
import * as THREE from "three";
import { onMounted, ref } from "vue";
import { DragControls, ThreeMFLoader } from "three/examples/jsm/Addons.js";

const canvas = ref<HTMLCanvasElement | null>(null);
const scene = new THREE.Scene();
const camera = new THREE.OrthographicCamera();
const planeGeo = new THREE.PlaneGeometry(2, 2);
const geometry = new THREE.BoxGeometry(0.25, 0.25, 1);
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00, transparent: true });
const cube = new THREE.Mesh(geometry, material);
const plane = new THREE.Mesh(
  planeGeo,
  new THREE.MeshBasicMaterial({ color: 0xffffff })
);
const gridHelper = new THREE.GridHelper(2, 8, 0x000000, 0x000000);
gridHelper.rotation.x = Math.PI / 2;
camera.position.z = 3;
gridHelper.position.z = 2;
plane.position.z = 0
cube.position
  .copy(new THREE.Vector3(0, 0, 1))
  .divideScalar(0.25)
  .floor()
  .multiplyScalar(0.25)
  .addScalar(0.125);

scene.add(cube);
scene.add(gridHelper);
scene.add(plane);

let objects: THREE.Object3D[] = [];
objects.push(cube);

onMounted(() => {
  const renderer = new THREE.WebGLRenderer({ canvas: canvas.value! });

  function render() {
    renderer.render(scene, camera);
  }

  const controls = new DragControls(objects, camera, renderer.domElement);
  renderer.setSize(640, 640);
  render();
  controls.addEventListener("hoveron", function (event) {
    if (event.object instanceof THREE.Mesh) {
      event.object.material.opacity = 0.5;
      render();
    }
  });

  controls.addEventListener("hoveroff", function (event) {
    if (event.object instanceof THREE.Mesh) {
      event.object.material.opacity = 1;
      render();
    }
  });
});
</script>
