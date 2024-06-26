<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4"><strong>InstaQL</strong></h1>
    <div v-if="loading" class="text-center">
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Chargement...</span>
      </div>
    </div>
    <div v-else>
      <div class="d-flex justify-content-between align-items-center mb-4">
        <h2 class="mb-0"><strong>Top Articles</strong></h2>
        <button class="btn btn-primary" @click="showModal = true">Ajouter un Article</button>
      </div>
      <div class="row">
        <div v-for="article in topArticles" :key="article.id" class="col-md-4 mb-4">
          <div class="card h-100" style="cursor: pointer;">
            <router-link :to="`/article/${article.id}`" class="text-decoration-none text-dark">
              <img v-if="article.imageUrl" :src="article.imageUrl" alt="Image de l'article"
                class="card-img-top article-image" />
              <div class="card-body">
                <h5 class="card-title">{{ article.title }}</h5>
                <p class="card-text"><i style="color: red;" class="fas fa-heart"></i> {{ article.likesCount }}</p>
              </div>
            </router-link>
          </div>
        </div>
      </div>
      <b-modal v-model="showModal" title="Ajouter un Article" hide-footer>
        <CreateArticleView @article-created="addArticle" @close="showModal = false" />
      </b-modal>

      <div class="d-flex justify-content-between align-items-center mt-5 mb-4">
        <h2 class="mb-0"><strong>Tous les Articles</strong></h2>
        <div>
          <button class="btn btn-secondary ml-2" @click="toggleSortOrder">
            Filtrer par Popularité
            <i :class="sortOrderIcon"></i>
          </button>
          &nbsp;
          <button class="btn btn-secondary ml-2" @click="resetFilter">Reset <i
              class="fa-solid fa-rotate-right"></i></button>
        </div>
      </div>
      <div class="scrollable-box">
        <div class="row">
          <div v-for="article in filteredArticles" :key="article.id" class="col-md-4 mb-4">
            <div class="card h-100" style="cursor: pointer;">
              <router-link :to="`/article/${article.id}`" class="text-decoration-none text-dark">
                <img v-if="article.imageUrl" :src="article.imageUrl" alt="Image de l'article"
                  class="card-img-top article-image" style="border-radius: 5px" />
                <div class=" card-body">
                  <h5 class="card-title">{{ article.title }}</h5>
                  <p class="card-text">{{ article.description }}</p>
                  <p class="card-text"><i style="color: red;" class="fas fa-heart"></i> {{ article.likes.length }}</p>
                </div>
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watchEffect, computed, onMounted } from 'vue';
import { useQuery } from '@vue/apollo-composable';
import { GET_TOP_ARTICLES, GET_ARTICLES } from '../graphql/queries';
import { GetTopArticlesQuery, GetArticlesQuery } from '../graphql/types';
import CreateArticleView from './CreateArticleView.vue';

export default defineComponent({
  components: {
    CreateArticleView,
  },
  setup() {
    const { loading: loadingTop, result: topResult } = useQuery<GetTopArticlesQuery>(GET_TOP_ARTICLES);
    const { loading: loadingAll, result: allResult, refetch: refetchAll } = useQuery<GetArticlesQuery>(GET_ARTICLES);

    const topArticles = ref<GetTopArticlesQuery['topArticles']>([]);
    const articles = ref<GetArticlesQuery['articles']>([]);
    const showModal = ref(false);
    const filterType = ref<string | null>(null);
    const sortOrder = ref<'asc' | 'desc'>('desc');

    watchEffect(() => {
      if (topResult.value) {
        topArticles.value = topResult.value.topArticles;
      }
    });

    watchEffect(() => {
      if (allResult.value) {
        articles.value = allResult.value.articles;
      }
    });

    onMounted(() => {
      refetchAll();
    });

    const addArticle = (newArticle: GetTopArticlesQuery['topArticles'][0]) => {
      topArticles.value.push(newArticle);
    };

    const toggleSortOrder = () => {
      sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
      applyFilter('likes');
    };

    const applyFilter = (type: string) => {
      filterType.value = type;
    };

    const resetFilter = () => {
      filterType.value = null;
    };

    const filteredArticles = computed(() => {
      let sortedArticles = articles.value;
      if (filterType.value === 'likes') {
        sortedArticles = [...articles.value].sort((a, b) => {
          const diff = b.likes.length - a.likes.length;
          return sortOrder.value === 'asc' ? -diff : diff;
        });
      }
      return sortedArticles;
    });

    const sortOrderIcon = computed(() => {
      return sortOrder.value === 'asc' ? 'fas fa-arrow-up' : 'fas fa-arrow-down';
    });

    return {
      loading: loadingTop || loadingAll,
      topArticles,
      articles,
      showModal,
      addArticle,
      applyFilter,
      resetFilter,
      filteredArticles,
      sortOrderIcon,
      toggleSortOrder,
    };
  },
});
</script>

<style scoped>
.article-image {
  width: 100%;
  height: auto;
  max-height: 200px;
  object-fit: cover;
}

.card {
  border: 1px solid #e3e3e3;
  transition: transform 0.2s;
}

.card:hover {
  transform: scale(1.02);
}

.scrollable-box {
  max-height: 400px;
  overflow-y: auto;
}

.card-title {
  font-size: 1.25rem;
  font-weight: bold;
}

.card-text {
  color: #555;
}
</style>
