<template>
  <canvas ref="canvas" width="640" height="640"></canvas>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
const canvas = ref<HTMLCanvasElement | null>(null);
onMounted(() => {
  const gl = canvas?.getContext("webgl");
  gl.clearColor(255.0, 255.0, 255.0, 1.0);
  gl.clear(gl.COLOR_BUFFER_BIT);
});
</script>
