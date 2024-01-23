<template>
  <div class="bg-black w-screen h-screen">
    <button @click="audioPlay">Test</button>
    <Canvas2D :destination="destination" :audio-src="audioSrc" />
  </div>
</template>

<script setup lang="ts">
import Canvas2D from "./components/Canvas2D.vue";
import { ref, watch, watchEffect } from "vue";
import { AudioType } from "./interfaces/audio";
let destination = [0, 0];
let audioSrc: Array<AudioType> = [
  { src: "/clang_sfx.mp3", point: [5, 5, 0] },
  { src: "/huh_sfx.mp3", point: [-2, -3, 0] },
];
let adMap: Map<string, any> = new Map();
const audioContext = new window.AudioContext();
audioSrc.forEach((element) => {
  const audioElement = new Audio(element.src);
  const track = audioContext.createMediaElementSource(audioElement);
  const pannerNode = audioContext.createPanner();
  watchEffect(() => {
    pannerNode.positionX.setValueAtTime(
      element.point[0],
      audioContext.currentTime
    );
    pannerNode.positionY.setValueAtTime(
      element.point[1],
      audioContext.currentTime
    );
  });
  track.connect(pannerNode).connect(audioContext.destination);
  adMap.set(element.src, audioElement);
});
// const testAudio = new Audio("/clang_sfx.mp3");
// const audioContext = new window.AudioContext();
// var track = audioContext.createMediaElementSource(testAudio);
// const gainNode = audioContext.createGain();
// const gainValue = ref(1);

// watch(gainValue, (value) => {
//   gainNode.gain.value = value;
// });
// track.connect(gainNode).connect(audioContext.destination);

function audioPlay() {
  if (audioContext.state === "suspended") audioContext.resume();

  if (adMap.get("/clang_sfx.mp3").paused) adMap.get("/clang_sfx.mp3").play();
  else {
    adMap.get("/clang_sfx.mp3").pause();
    adMap.get("/clang_sfx.mp3").currentTime = 0;
    adMap.get("/clang_sfx.mp3").play();
  }
}
</script>
