<template>
  <div class="image-container">
    <h1>Noise Generator</h1>
    <canvas ref="canvas" :height="HEIGHT" :width="WIDTH" />
  </div>
  <div class="sidebar col">
    <div class="row inputs">
      <div class="col w-full pt-7">
        <template v-for="(fn, idx) in noiseFns" :key="idx">
          <div class="row pt-3">
            <select class="select" v-model="fn.type">
              <option v-for="opt in options" :key="opt.key" :value="opt.name" selected>
                {{opt.name}}
              </option>
            </select>
          </div>
          <div class="row pt-3">
            <input v-model="fn.scale" type="number" step="5"/>
          </div>
          <div class="row justify-center pt-3">
            <button
              class="btn"
              @click="addAnother"
              v-if="idx === noiseFns.length - 1">
              +
            </button>
            <button
              class="btn bg-red-900"
              @click="remove(idx)"
              v-else>
              x
            </button>
          </div>
        </template>
      </div>
    </div>
    <div class="row pb-10">
      <button class="generate-btn" @click="generate">
        {{loading ? 'Loading...' : 'Generate'}}
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';

import { perlin2, simplex2, seed } from '@/utils/noise';

const HEIGHT = window.innerHeight - 100;
const WIDTH = window.innerWidth - 350;

export default defineComponent({
  name: 'Canvas',
  setup() {
    const canvas = ref<HTMLCanvasElement | null>(null);
    const defaultNoise = { type: 'Perlin', scale: 20 };
    const noiseFns = ref([defaultNoise])
    const loading = ref(true);

    const options = [
      {
        name: 'Perlin',
        fn: perlin2,
      },
      {
        name: 'Simplex',
        fn: simplex2,
      },
    ]

    onMounted(() => generate());

    function generate() {
      loading.value = true;

      seed(Math.random());

      const ctx = canvas.value?.getContext('2d');
      const image = ctx?.getImageData(0, 0, WIDTH, HEIGHT);
      const data = image?.data;

      if (data?.length && image && data) {

        for (let x = 0; x < WIDTH; x++) {
          for (let y = 0; y < HEIGHT; y++) {
            const cell = (x + y * WIDTH) * 4;

            let value = noiseFns.value.reduce((agg, noise) => {
              const fn = options.find(v => v.name === noise.type)?.fn;

              if (fn) {
                agg += Math.abs(fn(x / (noise.scale * 10), y / (noise.scale * 10)));
              }

              return agg;
            }, 0)

            value *= 256;

            updateColor(data, cell, value, value, value, 255);
          }
        }

        ctx?.putImageData(image, 0, 0);
      }

      loading.value = false;
    }

    function addAnother() {
      noiseFns.value.push({...defaultNoise});
    }

    function remove(idx: number) {
      noiseFns.value.splice(idx, 1);
    }

    return {
      noiseFns,
      canvas,
      HEIGHT,
      WIDTH,
      options,
      addAnother,
      remove,
      generate,
      loading
    }
  }
});

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
  @apply bg-gray-600 rounded px-5 py-2 text-white;
}

.generate-btn {
  @apply bg-purple-700 rounded px-5 py-2 w-full text-white;
}

.col {
  @apply flex flex-col;
}

.row {
  @apply flex;
}

.inputs {
  @apply flex-grow;
}

.select, .row > input {
  @apply py-2 px-3 w-full rounded outline-none bg-gray-900 text-white;
}

.sidebar {
  @apply flex-grow p-3 pl-0;
}

label {
  @apply text-white;
}
</style>
