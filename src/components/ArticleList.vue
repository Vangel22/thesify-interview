<template>
  <div class="container mx-auto p-4">
    <div class="flex justify-center items-center my-4 space-x-4">
      <div class="relative flex items-center w-[380px]">
        <img
          src="../assets/magnify.svg"
          alt="magnify icon"
          class="absolute left-3 w-5 h-5"
        />
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search for articles"
          :class="{
            'pl-10 pr-4 py-2 border rounded w-full': true,
            'cursor-not-allowed': filteredArticles.length === 0,
            'cursor-text': filteredArticles.length > 0,
          }"
        />
      </div>

      <select
        v-model="filter"
        @change="filterArticles"
        class="border rounded px-4 py-2"
      >
        <option value="all">All Articles</option>
        <option value="favorites">Favorites</option>
      </select>
    </div>

    <div v-if="filteredArticles.length === 0">
      <NoData msg="No articles found." />
    </div>
    <div class="flex flex-wrap -mx-2">
      <div
        v-for="article in filteredArticles"
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
            :isFavorite="article.isFavorite"
            @toggle-favorite="toggleFavorite"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { defineComponent, onMounted, ref, watch } from "vue";
import ArticleCard from "./ArticleCard.vue";
import NoData from "./NoData.vue";

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
    NoData,
  },
  setup() {
    const searchQuery = ref<String>("");
    const articles = ref<Article[]>([]);
    const favorites = ref<Article[]>(
      JSON.parse(localStorage.getItem("favorites") ?? "[]")
    );
    const filter = ref("all");
    const filteredArticles = ref<Article[]>([]);

    const searchInArticle = (article: Article, query: string): boolean => {
      const lowerCaseQuery = query.toLowerCase();
      const titleMatch = article.title?.toLowerCase().includes(lowerCaseQuery);
      const authorMatch = article.authors?.some((author) =>
        author.toLowerCase().includes(lowerCaseQuery)
      );

      const dateSetup = new Date(article.publicationDate);

      const dateMatch = dateSetup
        .toLocaleDateString()
        .toLowerCase()
        .includes(lowerCaseQuery);

      return titleMatch || authorMatch || dateMatch;
    };

    const fetchArticles = async () => {
      try {
        const query = searchQuery.value.trim().toLowerCase();
        const res = await axios.get(
          `https://api.openalex.org/works?search=${encodeURIComponent(query)}`
        );
        const dataFetched = res.data.results
          .map((item: Partial<Article | any>) => ({
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
            isFavorite: favorites.value.some(
              (fav: Article) => fav.id === item.id
            ),
          }))
          .sort((a: Article, b: Article) => {
            return b.publicationDate.getTime() - a.publicationDate.getTime();
          });

        articles.value = dataFetched;
      } catch (error) {
        console.error("Failed to fetch articles:", error);
      }
    };

    const fetchFavorites = () => {
      const query = searchQuery.value.trim().toLowerCase();

      const filteredFavoriteArticles = favorites.value.filter((f) =>
        searchInArticle(f, query)
      );
      filteredArticles.value = filteredFavoriteArticles;
    };

    const filterArticles = () => {
      if (filter.value === "favorites") {
        const storedFavorites = JSON.parse(
          localStorage.getItem("favorites") ?? "[]"
        );
        favorites.value = storedFavorites;
        fetchFavorites();
      } else {
        filteredArticles.value = articles.value.filter((article) =>
          searchInArticle(article, searchQuery.value.trim())
        );
      }
    };

    const toggleFavorite = (articleId: string) => {
      const articleIndex = articles.value.findIndex(
        (article: Article) => article.id === articleId
      );
      const foundArticle = articles.value[articleIndex];
      if (articleIndex !== -1) {
        articles.value[articleIndex].isFavorite = !foundArticle.isFavorite;

        if (foundArticle.isFavorite) {
          favorites.value.push(foundArticle);
        } else {
          favorites.value = favorites.value.filter(
            (article) => article.id !== articleId
          );
        }
      }

      localStorage.setItem("favorites", JSON.stringify(favorites.value));
      filterArticles();
    };

    onMounted(() => {
      fetchArticles();
      filterArticles();
    });

    watch([filter, articles], () => {
      filterArticles();
    });

    watch(searchQuery, () => {
      fetchArticles();
    });

    return {
      searchQuery,
      filter,
      articles,
      filteredArticles,
      fetchArticles,
      fetchFavorites,
      filterArticles,
      toggleFavorite,
    };
  },
});
</script>
