<template>
  <div class="bg-black w-screen h-screen">
    <button class="bg-white rounded-xl py-2 px-5" @click="audioPlay">
      Test
    </button>
    <input type="range" max="2" min="0" step="0.1" v-model="gainValue" />
    <input type="range" />
    <Canvas2D />
  </div>
</template>

<script setup lang="ts">
import Canvas2D from './components/Canvas2D.vue'
import { ref, watch } from "vue";
const testAudio = new Audio("/clang_sfx.mp3");
const audioContext = new window.AudioContext();
var track = audioContext.createMediaElementSource(testAudio);
const gainNode = audioContext.createGain();
const gainValue = ref(1);

watch(gainValue, (value) => {
  gainNode.gain.value = value;
});
track.connect(gainNode).connect(audioContext.destination);

function audioPlay() {
  if (audioContext.state === "suspended") audioContext.resume();

  if (testAudio.paused) testAudio.play();
  else {
    testAudio.pause();
    testAudio.currentTime = 0;
    testAudio.play();
  }
}
</script>
