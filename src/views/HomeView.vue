<template>
  <div>
    <Player />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Player from '@/components/Player.vue';
export default defineComponent({
  name: 'HomeView',
  components: {
    Player,
  },
});
</script>
