<template>
  <h1>Noise Generator</h1>
  <Canvas />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Canvas from '@/components/canvas.vue';

export default defineComponent({
  name: 'App',
  components: {
    Canvas
  }
});
</script>

