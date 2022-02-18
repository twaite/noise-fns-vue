<template>
  <nf-canvas />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import NfCanvas from '@/components/nf-canvas.vue';

export default defineComponent({
  name: 'App',
  components: {
    NfCanvas
  }
});
</script>

<style lang="postcss">
#app {
  @apply h-screen w-screen bg-gray-800 flex;
}
</style>
