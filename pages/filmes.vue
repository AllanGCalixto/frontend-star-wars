<template>
  <div class="star-wars-theme">
    <!-- Navbar -->
    <nav class="navbar">
      <ul>
        <li><nuxt-link to="/">Home</nuxt-link></li>
        <li><nuxt-link to="/planetas">Planetas</nuxt-link></li>
        <li><nuxt-link to="/usuarios">Usuários</nuxt-link></li>
        <li><a href="#" @click.prevent="handleLogout">Logout</a></li>
      </ul>
    </nav>

    <!-- Conteúdo da página de filmes -->
    <div class="filmes-page">
      <h1>Filmes</h1>

      <!-- Estado de carregamento -->
      <div v-if="isLoading" class="loading">
        <p>Carregando filmes...</p>
      </div>

      <!-- Mensagem de erro -->
      <div v-else-if="errorMessage" class="error">
        <p>{{ errorMessage }}</p>
      </div>

      <!-- Lista de filmes -->
      <div v-else>
        <ul class="filmes-list">
          <li v-for="filme in filmes" :key="filme.id" class="filme-item">
            <h2>{{ filme.title }}</h2>
            <p><strong>Diretor:</strong> {{ filme.director }}</p>
            <p><strong>Ano:</strong> {{ filme.release_date }}</p>
            <p>{{ filme.opening_crawl }}</p>
          </li>
        </ul>
      </div>

      <!-- Botão de logout (opcional, já presente na navbar) -->
      <!-- <button @click="handleLogout" class="logout-button">Logout</button> -->
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter, useCookie } from '#imports' // ou '#app'

const router = useRouter()
const tokenCookie = useCookie('auth_token')

const filmes = ref([])
const isLoading = ref(true)
const errorMessage = ref('')

// Função para fazer logout
function handleLogout() {
  tokenCookie.value = null
  router.push('/login')
}

// Função para buscar filmes
async function fetchFilmes() {
  if (!tokenCookie.value) {
    router.push('/login')
    return
  }

  try {
    const response = await $fetch('http://localhost:3000/star-wars/films', {
      method: 'GET',
      headers: {
        Authorization: `Bearer ${tokenCookie.value}`,
      },
    })
    filmes.value = response
  } catch (error) {
    errorMessage.value = error.message || 'Ocorreu um erro ao buscar os filmes.'
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchFilmes()
})
</script>

<style scoped>
.star-wars-theme {
  background-image: 
    url('/images/Starwars.webp'),
    radial-gradient(white 0.5px, transparent 0.5px),
    radial-gradient(white 0.5px, transparent 0.5px);
  background-color: black; 
  background-size: cover, 100px 100px, 100px 100px; 
  background-position: center, 0 0, 50px 50px; 
  background-repeat: no-repeat, repeat, repeat; 
  
  color: yellow; 
  min-height: 100vh;
  display: flex;
  flex-direction: column; 
  align-items: center;
  justify-content: flex-start; 
  font-family: 'Star Jedi', sans-serif; 
  position: relative;
  overflow: hidden;
}

.navbar {
  width: 100%;
  background-color: rgba(0, 0, 0, 0.7); 
  padding: 15px 0;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 2; 
}

.navbar ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center; 
}

.navbar ul li {
  margin: 0 20px;
}

.navbar ul li a {
  color: yellow;
  text-decoration: none;
  font-size: 1.1em;
  transition: color 0.3s;
}

.navbar ul li a:hover {
  color: rgba(255, 255, 0, 0.8);
}

.filmes-page {
  background-color: rgba(0, 0, 0, 0.6); 
  padding: 100px 40px 40px 40px; 
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(255, 255, 0, 0.5);
  text-align: center;
  width: 90%;
  max-width: 800px;
  position: relative;
  z-index: 1;
}

.filmes-page h1 {
  margin-bottom: 30px;
  font-size: 2.5em;
  text-shadow: 2px 2px 4px #000;
}

.loading,
.error {
  font-size: 1.2em;
  margin-top: 20px;
  color: red; 
}

.filmes-list {
  list-style-type: none;
  padding: 0;
}

.filme-item {
  background-color: rgba(0, 0, 0, 0.5);
  margin: 15px 0;
  padding: 20px;
  border-radius: 8px;
  text-align: left;
  transition: transform 0.3s, background-color 0.3s;
}

.filme-item:hover {
  transform: scale(1.02);
  background-color: rgba(255, 255, 0, 0.1);
}

.filme-item h2 {
  margin-bottom: 10px;
  font-size: 1.8em;
  text-shadow: 1px 1px 2px #000;
}

.filme-item p {
  margin: 5px 0;
  line-height: 1.5;
}

.logout-button {
  margin-top: 30px;
  padding: 10px 20px;
  background-color: yellow;
  color: black;
  border: none;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s;
  font-size: 1em;
}

.logout-button:hover {
  background-color: rgba(255, 255, 0, 0.8);
}
</style>