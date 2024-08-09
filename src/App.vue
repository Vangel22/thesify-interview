<template>
  <ArticleList />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import "./index.css";
import ArticleList from "./components/ArticleList.vue";

export default defineComponent({
  name: "App",
  components: {
    ArticleList,
  },
});
</script>
