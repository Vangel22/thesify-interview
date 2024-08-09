<template>
  <div class="flex flex-wrap -mx-2">
    <div
      v-for="article in articles"
      :key="article.id"
      class="w-full md:w-1/3 px-2 mb-4"
    >
      <div class="flex flex-col h-full">
        <ArticleCard
          :id="article.id"
          :title="article.title"
          :summary="article.abstract"
          :authors="article.authors"
          :concepts="article.concepts.slice(0, 6)"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { defineComponent, onMounted, ref } from "vue";
import ArticleCard from "./ArticleCard.vue";

interface Concept {
  display_name: string;
}

interface Article {
  id: string;
  title: string;
  abstract: string;
  concepts: Concept[];
  isFavorite: boolean;
  authors: string[];
  publicationDate: Date;
}

export default defineComponent({
  name: "ArticleList",
  components: {
    ArticleCard,
  },
  setup() {
    const articles = ref<Article[]>([]);

    const fetchArticles = async () => {
      const res = await axios.get(`https://api.openalex.org/works`);
      const dataFetched = res.data.results.map((item: any) => ({
        id: item.id,
        title: item.title,
        abstract: item.abstract ?? "No abstract available.",
        concepts: item.concepts
          ? item.concepts.map((concept: Concept) => concept.display_name)
          : [],
        authors: item.authorships
          ? item.authorships.map((data: any) => data.author.display_name)
          : [],
        publicationDate: new Date(item.publication_date),
      }));

      articles.value = dataFetched;
    };

    onMounted(() => {
      fetchArticles();
    });

    return {
      articles,
      fetchArticles,
    };
  },
});
</script>
