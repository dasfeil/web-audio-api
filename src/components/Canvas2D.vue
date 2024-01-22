<template>
  <canvas ref="canvas" class="m-auto" width="640" height="640"> </canvas>
</template>

<script setup lang="ts">
import * as THREE from "three";
import { onMounted, ref } from "vue";
import { DragControls } from "three/examples/jsm/Addons.js";

const canvas = ref<HTMLCanvasElement | null>(null);
const scene = new THREE.Scene();
const camera = new THREE.OrthographicCamera();
const planeGeo = new THREE.PlaneGeometry(2, 2);
const plane = new THREE.Mesh(
  planeGeo,
  new THREE.MeshBasicMaterial({ color: 0xffffff })
);
const geometry = new THREE.BoxGeometry(0.08, 0.08, 1);
const material = new THREE.MeshBasicMaterial({
  color: 0x00ff00,
  transparent: true,
});
const audioDestination = new THREE.Mesh(geometry.clone(), material.clone());
const gridHelper = new THREE.GridHelper(2, 25, 0x000000, 0x000000);
gridHelper.rotation.x = Math.PI / 2;
camera.position.z = 3;
gridHelper.position.z = 2;
plane.position.z = 0;
audioDestination.position
  .copy(new THREE.Vector3(0, 0, 1))
  .divideScalar(0.08)
  .floor()
  .multiplyScalar(0.08)

scene.add(audioDestination, gridHelper, plane);

let objects: THREE.Object3D[] = [];
objects.push(audioDestination);

onMounted(() => {
  const renderer = new THREE.WebGLRenderer({ canvas: canvas.value! });

  function render() {
    renderer.render(scene, camera);
  }

  const controls = new DragControls(objects, camera, renderer.domElement);
  renderer.setSize(640, 640);
  render();
  controls.addEventListener("hoveron", (event) => {
    if (event.object instanceof THREE.Mesh) {
      event.object.material.opacity = 0.5;
      render();
    }
  });

  controls.addEventListener("hoveroff", (event) => {
    if (event.object instanceof THREE.Mesh) {
      event.object.material.opacity = 1;
      render();
    }
  });

  let tempNode: THREE.Mesh | undefined;

  controls.addEventListener("dragstart", (event) => {
    if (event.object instanceof THREE.Mesh && tempNode == undefined) {
      tempNode = new THREE.Mesh(geometry.clone(), material.clone());
      tempNode.position.copy(event.object.position);
      scene.add(tempNode);
    }
  });

  controls.addEventListener("drag", (event) => {
    let raycaster = controls.getRaycaster();
    let intersect = raycaster.intersectObject(plane);
    if (intersect[0]) {
      event.object.position
        .divideScalar(0.08)
        .floor()
        .multiplyScalar(0.08)
    }
    render();
  });

  controls.addEventListener("dragend", (event) => {
    if (tempNode !== undefined) {
      tempNode.geometry.dispose();
      (tempNode.material as THREE.Material).dispose();
      scene.remove(tempNode);
      tempNode = undefined;
    }
    render();
  });
});
</script>
