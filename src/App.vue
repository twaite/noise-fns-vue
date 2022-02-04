<template>
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

<style lang="postcss">
#app {
  @apply h-screen w-screen bg-gray-800 flex;
}
</style>
