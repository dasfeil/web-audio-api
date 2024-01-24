<template>
  <div class="bg-black w-screen h-screen">
    <div class="w-fit m-auto flex flex-col">
      <div class="text-white">
        <p>Current Position:</p>
        <div class="ml-5">
          <p>
            Audio Destination: [{{
              destination[0] / 0.08 + ", " + destination[2] / 0.08
            }}]
          </p>
          <div v-for="(src, index) in audioSrc">
            <p>
              Source {{ index + 1 }}: [{{
                src.point[0] / 0.08 + ", " + src.point[2] / 0.08
              }}]
            </p>
          </div>
        </div>
      </div>
      <Canvas2D
        :destination="destination"
        :audio-src="audioSrc"
        @dragged="modifySource"
      />
      <button
        class="text-black rounded-md bg-[#ffffff] w-16 p-1 self-center mt-2"
        @click="startAudio"
      >
        Test
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import Canvas2D from "./components/Canvas2D.vue";
import { watchEffect, ref } from "vue";
let destination = ref([0, 0, 0]);
let audioSrc = ref([
  { src: "/clang_sfx.mp3", point: [0, 0, 0] },
  { src: "/huh_sfx.mp3", point: [0, 0, 0] },
]);
let adMap: Map<string, any> = new Map();
const audioContext = new window.AudioContext();
watchEffect(() => {
  const listener = audioContext.listener;
  listener.positionX.value = destination.value[0];
  listener.positionY.value = destination.value[1];
  listener.positionZ.value = destination.value[2];
  listener.forwardX.value = 0;
  listener.forwardY.value = 1;
  listener.forwardZ.value = 0;
  listener.upX.value = 0;
  listener.upY.value = 0;
  listener.upZ.value = 1;
});

function modifySource(name: string, coord: THREE.Vector3) {
  if (!name) {
    destination.value = [coord.x, coord.z, coord.y];
    return;
  }
  audioSrc.value.filter((element) => element.src == name)[0].point = [
    coord.x,
    coord.z,
    coord.y,
  ];
}

function startAudio() {
  if (audioContext.state === "suspended") {
    audioContext.resume();
  }

  adMap.forEach((element: HTMLMediaElement) => {
    element.loop = true;
    element.play();
  });
}

audioSrc.value.forEach((element) => {
  const audioElement = new Audio(element.src);
  const track = audioContext.createMediaElementSource(audioElement);
  const pannerNode = audioContext.createPanner();
  pannerNode.panningModel = "HRTF";
  watchEffect(() => {
    pannerNode.positionX.setValueAtTime(
      element.point[0],
      audioContext.currentTime
    );
    pannerNode.positionY.setValueAtTime(
      element.point[1],
      audioContext.currentTime
    );
    pannerNode.positionZ.setValueAtTime(
      element.point[2],
      audioContext.currentTime
    );
  });
  track.connect(pannerNode).connect(audioContext.destination);
  adMap.set(element.src, audioElement);
});
</script>
