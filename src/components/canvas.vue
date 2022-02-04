<template>
  <div class="image-container">
    <h1>Noise Generator</h1>
    <canvas ref="canvas" :height="HEIGHT" :width="WIDTH" />
  </div>
  <div class="sidebar">
    <div class="row">
      <button class="btn" @click="generateRandomNoise">Generate Random Noise</button>
      <button class="btn" @click="generatePerlinNoise">Generate Perlin Noise</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue';
import { perlin2 } from '@/utils/perlin';

const HEIGHT = window.innerHeight - 100;
const WIDTH = window.innerWidth - 350;

export default defineComponent({
  name: 'Canvas',
  setup() {
    const canvas = ref<HTMLCanvasElement | null>(null);

    onMounted(() => {
      generateRandomNoise();
    })

    function generateRandomNoise() {
      const ctx = canvas.value?.getContext('2d');
      const image = ctx?.getImageData(0, 0, WIDTH, HEIGHT);
      const data = image?.data;

      if (data?.length && image && data) {

        for (var i = 0; i < data.length; i += 4) {
          updateColor(data, i, randomInt(0, 255), randomInt(0, 255), randomInt(0, 255), 255);
        }

        ctx?.putImageData(image, 0, 0);
      }
    }

    function generatePerlinNoise() {
      const ctx = canvas.value?.getContext('2d');
      const image = ctx?.getImageData(0, 0, WIDTH, HEIGHT);
      const data = image?.data;

      if (data?.length && image && data) {

        for (let x = 0; x < WIDTH; x++) {
          for (let y = 0; y < HEIGHT; y++) {
            const cell = (x + y * WIDTH) * 4;

            let value = Math.abs(perlin2(x / 100, y / 100));
            value *= 256;

            updateColor(data, cell, value, value, value, 255);
          }
        }

        ctx?.putImageData(image, 0, 0);
      }
    }

    return {
      canvas,
      HEIGHT,
      WIDTH,
      generateRandomNoise,
      generatePerlinNoise
    }
  }
});

function randomInt(min: number, max: number) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function updateColor(data: Uint8ClampedArray, cell: number, r: number, g: number, b: number, a = 255) {
  data[cell] = r;
  data[cell+1] = g;
  data[cell+2] = b;
  data[cell+3] = a;
}
</script>

<style lang="postcss">
.image-container {
  @apply p-3;
}

.image-container > h1 {
  @apply text-2xl text-white font-bold pb-2;
}

.btn {
  @apply bg-purple-500 rounded px-3 py-2;
}
</style>
