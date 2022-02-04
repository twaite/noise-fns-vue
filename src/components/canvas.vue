<template>
  <canvas ref="canvas" :height="HEIGHT" :width="WIDTH" />
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue';

const HEIGHT = 800;
const WIDTH = 800;

export default defineComponent({
  name: 'Canvas',
  setup() {
    const canvas = ref<HTMLCanvasElement | null>(null);

    onMounted(() => {
      const ctx = canvas.value?.getContext('2d');
      const image = ctx?.getImageData(0, 0, WIDTH, HEIGHT);
      const data = image?.data;

      if (data?.length && image && data) {

        for (var i = 0; i < data.length; i += 4) {
          updateColor(data, i, randomInt(0, 255), randomInt(0, 255), randomInt(0, 255), 255);
        }

        ctx?.putImageData(image, 0, 0);
      }
    })

    return {
      canvas,
      HEIGHT,
      WIDTH
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
