<script setup lang="ts">
import { onMounted, ref } from "vue";

const props = defineProps<{ time: number }>();

let currentTime = props.time * 1000;
let end: number | null = Date.now() + currentTime;
let interval = 0;

const timer = ref<string>();
const isRunning = ref<boolean>(false);

onMounted(() => {
  setTime(currentTime);
});

function onResetClickHandle() {
  currentTime = props.time * 1000;
  end = Date.now() + currentTime;
  setTime(currentTime);
}

function onStopClickHandle() {
  stop();
}

function onPlayClickHandle() {
  isRunning.value = true;
  end = Date.now() + currentTime;
  start();
}

function setTime(time: number) {
  const min = Math.floor(time / 1000 / 60);
  const sec = (time / 1000) % 60;
  const mil = (time % 1000) / 10;
  timer.value = min ? `${pad(min)}:${pad(sec)}` : `${pad(sec)}.${pad(mil)}`;
}

function pad(time: number) {
  return String(Math.floor(time)).padStart(2, "0");
}

function start() {
  currentTime = Math.max(0, end! - Date.now());
  setTime(currentTime);
  interval = requestAnimationFrame(() => start());
  if (currentTime === 0) {
    stop();
  }
  return;
}

function stop() {
  cancelAnimationFrame(interval);
  end = null;
  isRunning.value = false;
}
</script>
<template>
  <div class="d-flex flex-column">
    <div>
      <p class="font-weight-black">{{ timer }}</p>
    </div>
    <div class="d-flex flex-row gap justify-center">
      <v-btn v-if="!isRunning && currentTime > 0" @click="onPlayClickHandle">
        play
      </v-btn>
      <v-btn
        v-else-if="isRunning && currentTime > 0"
        @click="onStopClickHandle"
      >
        stop
      </v-btn>
      <v-btn @click="onResetClickHandle"> reset </v-btn>
    </div>
  </div>
</template>
