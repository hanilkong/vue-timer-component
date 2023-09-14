<script setup lang="ts">
import { onMounted, ref } from "vue";

const props = defineProps<{ time: number }>();

let currentTime = props.time * 1000;
let end: number | null = Date.now() + currentTime;
let interval = 0;
let sec = String(Math.floor(currentTime / 1000)).padStart(2, "0");
let mil = String(Math.floor((currentTime % 1000) / 10)).padStart(2, "0");

const timer = ref<string>(`${sec}.${mil}`);
const isRunning = ref<boolean>(false);

onMounted(() => {});

function onResetClickHandle() {
  currentTime = props.time * 1000;
  end = Date.now() + currentTime;
  setTime(currentTime);
}

function onStopClickHandle() {
  cancelAnimationFrame(interval);
  end = null;
  isRunning.value = false;
}

function onPlayClickHandle() {
  isRunning.value = true;
  end = Date.now() + currentTime;
  start();
}

function setTime(time: number) {
  sec = String(Math.floor(time / 1000)).padStart(2, "0");
  mil = String(Math.floor((time % 1000) / 10)).padStart(2, "0");
  timer.value = `${sec}.${mil}`;
}

function start() {
  currentTime = Math.max(0, end! - Date.now());
  setTime(currentTime);
  interval = requestAnimationFrame(() => start());
  if (currentTime === 0) {
    cancelAnimationFrame(interval);
    return;
  }
}
</script>
<template>
  <div class="d-flex flex-column">
    <div>
      <p class="font-weight-black">{{ timer }}</p>
    </div>
    <div class="d-flex flex-row gap justify-center">
      <v-btn v-if="!isRunning" @click="onPlayClickHandle" variant="tonal">
        play
      </v-btn>
      <v-btn v-else @click="onStopClickHandle" variant="tonal"> stop </v-btn>
      <v-btn @click="onResetClickHandle" variant="tonal"> reset </v-btn>
    </div>
  </div>
</template>
