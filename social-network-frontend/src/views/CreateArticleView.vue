<template>
  <div class="container mt-4">
    <form @submit.prevent="createArticle">
      <div class="mb-3">
        <label for="title" class="form-label">Titre :</label>
        <input type="text" class="form-control" v-model="title" required maxlength="25" />
        <small class="form-text text-muted">Max 25 caractères</small>
      </div>
      <div class="mb-3">
        <label for="description" class="form-label">Description :</label>
        <textarea class="form-control" v-model="description" rows="3" required maxlength="100"></textarea>
        <small class="form-text text-muted">Max 100 caractères</small>
      </div>
      <div class="mb-3">
        <label for="imageUrl" class="form-label">URL de l'image :</label>
        <input type="text" class="form-control" v-model="imageUrl" />
      </div>

      <button type="submit" class="btn btn-primary w-100">
        <i class="fas fa-check"></i> Post
      </button>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { useMutation } from '@vue/apollo-composable';
import { CREATE_ARTICLE } from '../graphql/mutations';

export default defineComponent({
  setup() {
    const title = ref('');
    const description = ref('');
    const imageUrl = ref('');
    const { mutate: createArticleMutation } = useMutation(CREATE_ARTICLE);

    const createArticle = async () => {
      try {
        const response = await createArticleMutation({
          title: title.value,
          description: description.value,
          imageUrl: imageUrl.value,
        });
        if (response && response.data && response.data.createArticle) {
          alert("Post Crée !");
          window.location.href = '/';
        }
      } catch (error) {
        alert(error);
        console.error('Erreur lors de la création de la article:', error);
      }
    };

    return {
      title,
      description,
      imageUrl,
      createArticle,
    };
  },
});
</script>

<style scoped>
.container {
  max-width: 600px;
}
</style>
