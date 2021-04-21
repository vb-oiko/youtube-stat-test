<template>
  <div
    class="bg-white px-4 pt-8 sm:px-20 sm:pb-8 sm:pt-16 sm:rounded-lg sm:shadow-lg sm:max-w-lg sm:h-120"
  >
    <h2
      class="text-2xl font-bold leading-7 text-gray-900 sm:text-3xl sm:truncate"
    >
      YouTube Channel Stats
    </h2>

    <form action="submit" @submit.prevent="onSubmit">
      <label for="email" class="mt-10 block text-sm font-medium text-gray-700"
        >YouTube Channel ID</label
      >
      <div class="mt-1">
        <input
          type="text"
          name="channelId"
          id="channelId"
          class="shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full sm:text-sm border-gray-300 rounded-md"
          :placeholder="DEFAULT_CHANNEL_ID"
          v-model="id"
          ref="inputEl"
        />
      </div>
    </form>

    <div class="sm:h-60 mt-5 sm:overflow-y-auto">
      <div v-if="data.status.start" class="text-gray-500">
        Please type in some YouTube channel id
      </div>
      <div v-if="data.status.error" class="text-red-300">
        Whoops, something went wrong. <br />Please check if the id is correct
      </div>
      <div v-if="data.status.loading">Loading...</div>

      <div v-show="data.status.success">
        <h3 class="text-lg leading-6 font-medium text-gray-900 mb-5">
          Channel Stats
        </h3>
        <div class="border-t border-gray-200 px-4 py-5 sm:p-0">
          <dl class="sm:divide-y sm:divide-gray-200">
            <div
              v-for="(item, ind) in data.items"
              :key="ind"
              class="py-4 sm:py-5 sm:grid sm:grid-cols-3 sm:gap-4"
            >
              <dt class="text-sm font-medium text-gray-500">
                {{ item.label }}
              </dt>
              <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">
                {{ item.value }}
              </dd>
            </div>
          </dl>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent, reactive } from "vue";
const API_KEY = import.meta.env.VITE_YOUTUBE_API_KEY;
const DEFAULT_CHANNEL_ID = import.meta.env.VITE_DEFAULT_CHANNEL_ID;

export default defineComponent({
  name: "YoutubeStatsPage",

  mounted() {
    this.inputEl.focus();
  },

  setup: () => {
    const inputEl = ref(null);
    const id = ref("");
    const data = reactive({
      items: [] as { label: string; value: string | number }[],
      status: {
        start: true,
        error: false,
        loading: false,
        success: false,
      } as {
        start?: boolean;
        error?: boolean;
        loading?: boolean;
        success?: boolean;
      },
    });

    const onSubmit = async () => {
      if (id.value === "") {
        id.value = DEFAULT_CHANNEL_ID as string;
      }

      data.status = { loading: true };
      const requestUrl = `https://www.googleapis.com/youtube/v3/channels?id=${DEFAULT_CHANNEL_ID}&key=${API_KEY}&part=brandingSettings,statistics`;

      const response = await fetch(requestUrl).catch(() => {
        data.status = { error: true };
      });

      if (response === undefined) {
        return;
      }

      const stats = await response.json().catch(() => {
        data.status = { error: true };
      });

      if (stats && stats.items && stats.items.length) {
        const {
          brandingSettings: {
            channel: { title },
          },
          statistics: { videoCount, viewCount },
        } = stats.items[0];

        data.items = [
          { label: "Channel Name", value: title },
          { label: "Total Views", value: viewCount },
          { label: "Average Views", value: Math.round(viewCount / videoCount) },
        ];
        data.status = { success: true };
      }
    };

    return { inputEl, id, data, onSubmit, DEFAULT_CHANNEL_ID };
  },
});
</script>
