<template>
  <div class="bg-gradient-to-r from-gray-200 to-gray-300 w-screen h-screen grid place-content-center">
    <YoutubeStatsPage />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import YoutubeStatsPage from "./components/YoutubeStatsPage.vue";

export default defineComponent({
  name: "App",
  components: {
    YoutubeStatsPage,
  },
});
</script>
