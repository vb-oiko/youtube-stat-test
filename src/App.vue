<template>
  <div class="sm:bg-gradient-to-r sm:from-gray-200 sm:to-gray-300 sm:w-screen sm:h-screen sm:grid sm:place-content-center">
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
