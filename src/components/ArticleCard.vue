<template>
  <div
    class="border max-w-sm rounded overflow-hidden shadow-lg flex flex-col h-full"
  >
    <div class="px-6 py-4 flex-grow">
      <div class="font-bold text-xl mb-2">{{ title }}</div>
      <p class="text-gray-700 text-base">{{ summary }}</p>
    </div>

    <div class="flex flex-col px-6 pt-4 pb-2">
      <span>Authors</span>
      <span
        v-for="author in authors"
        :key="author"
        class="inline-block bg-orange-200 rounded px-3 py-1 text-sm font-semibold text-gray-700 mr-2 mb-2"
      >
        {{ author }}
      </span>
    </div>

    <div class="px-6 pt-4 pb-2">
      <span
        v-for="concept in concepts"
        :key="concept.display_name"
        class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2 mb-2"
      >
        #{{ concept }}
      </span>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";

export default defineComponent({
  name: "ArticleCard",
  props: {
    id: {
      type: String,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    summary: {
      type: String,
      required: true,
    },
    authors: {
      type: Array as PropType<string[]>,
      required: true,
    },
    concepts: {
      type: Array as PropType<{ display_name: string }[]>,
      required: true,
    },
  },
});
</script>
