<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
      <router-link class="navbar-brand" to="/">InstaQL</router-link>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav"
        aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto">
          <li class="nav-item">
            <router-link class="nav-link" to="/">Accueil</router-link>
          </li>
          <li v-if="!isLoggedIn" class="nav-item">
            <router-link class="nav-link" to="/login">Connexion</router-link>
          </li>
          <li v-if="isLoggedIn" class="nav-item">
            <router-link class="nav-link" to="/my-articles">Mes Articles</router-link>
          </li>
          <li v-if="isLoggedIn" class="nav-item">
            <button class="nav-link btn btn-link" @click="logout">Déconnexion</button>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue';

export default defineComponent({
  setup() {
    const isLoggedIn = computed(() => {
      return !!localStorage.getItem('token');
    });

    const logout = () => {
      localStorage.removeItem('token');
      window.location.href = '/';
    };

    return {
      isLoggedIn,
      logout,
    };
  },
});
</script>

<style scoped>
.navbar {
  margin-bottom: 1rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.navbar-brand {
  font-size: 1.75rem;
  font-weight: bold;
  color: #f8f9fa !important;
  transition: color 0.3s;
}

.navbar-brand:hover {
  color: #d3d3d3 !important;
}

.nav-link {
  font-size: 1.1rem;
  color: #f8f9fa !important;
  transition: color 0.3s;
}

.nav-link:hover {
  color: #d3d3d3 !important;
}

.btn-link {
  color: #f8f9fa !important;
  font-size: 1.1rem;
  transition: color 0.3s, text-decoration 0.3s;
}

.btn-link:hover {
  text-decoration: underline;
  color: #d3d3d3 !important;
}

.navbar-toggler {
  border: none;
}

.navbar-toggler-icon {
  filter: brightness(0) invert(1);
}
</style>
