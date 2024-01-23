<template>
  <div class="bg-black w-screen h-screen">
    <div class="w-fit m-auto">
      <div class="text-white">
        <p>Current Position:</p>
        <div class="ml-5">
          <p>
            Audio Destination: [{{ destination[0] + ", " + destination[2] }}]
          </p>
          <div v-for="(src, index) in audioSrc">
            <p>
              Source {{ index }}: [{{ src.point[0] + ", " + src.point[2] }}]
            </p>
          </div>
        </div>
      </div>
      <Canvas2D :destination="destination" :audio-src="audioSrc" />
    </div>
  </div>
</template>

<script setup lang="ts">
import Canvas2D from "./components/Canvas2D.vue";
import { watchEffect } from "vue";
import { AudioType } from "./interfaces/audio";
let destination = [0, 0, 0];
let audioSrc: Array<AudioType> = [
  { src: "/clang_sfx.mp3", point: [1, 1, 0] },
  { src: "/huh_sfx.mp3", point: [1, 1, 1] },
];
let adMap: Map<string, any> = new Map();
const audioContext = new window.AudioContext();
audioSrc.forEach((element) => {
  const audioElement = new Audio(element.src);
  const track = audioContext.createMediaElementSource(audioElement);
  const pannerNode = audioContext.createPanner();
  pannerNode.panningModel = "HRTF";
  watchEffect(() => {
    pannerNode.positionX.setValueAtTime(
      element.point[0],
      audioContext.currentTime
    );
    pannerNode.positionZ.setValueAtTime(
      element.point[1],
      audioContext.currentTime
    );
  });
  track.connect(pannerNode).connect(audioContext.destination);
  adMap.set(element.src, audioElement);
});
</script>
