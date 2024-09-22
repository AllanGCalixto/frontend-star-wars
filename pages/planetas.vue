<template>
  <div class="star-wars-theme">
    <!-- Navbar -->
    <nav class="navbar">
      <ul>
        <li><nuxt-link to="/">Home</nuxt-link></li>
        <li><nuxt-link to="/filmes">Filmes</nuxt-link></li>
        <li><nuxt-link to="/usuarios">Usuários</nuxt-link></li>
        <li><a href="#" @click.prevent="handleLogout">Logout</a></li>
      </ul>
    </nav>

    <!-- Conteúdo da página -->
    <div class="content">
      <h1>Planetas</h1>
      
      <div v-if="isLoading" class="loading">
        <p>Carregando planetas...</p>
      </div>

      <div v-else-if="errorMessage" class="error">
        <p>{{ errorMessage }}</p>
      </div>

      <div v-else>
        <ul class="planetas-list">
          <li v-for="planeta in planetas" :key="planeta.id">
            <h2>{{ planeta.name }}</h2>
            <p><strong>Clima:</strong> {{ planeta.climate }}</p>
            <p><strong>Terreno:</strong> {{ planeta.terrain }}</p>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useCookie, useRouter } from '#imports' // ou '#app'

const tokenCookie = useCookie('auth_token')
const token = tokenCookie.value
const router = useRouter()

const planetas = ref([])
const isLoading = ref(true)
const errorMessage = ref('')

// Função de logout
function handleLogout() {
  tokenCookie.value = null
  router.push('/login')
}

onMounted(async () => {
  if (!token) {
    router.push('/login')
    return
  }

  try {
    const response = await $fetch('http://localhost:3000/star-wars/planets', {
      method: 'GET',
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })

    planetas.value = response
  } catch (error) {
    if (error.response && error.response._data && error.response._data.message) {
      errorMessage.value = error.response._data.message
    } else {
      errorMessage.value = 'Ocorreu um erro ao buscar os planetas.'
    }
  } finally {
    isLoading.value = false
  }
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

.content {
  text-align: center;
  margin-top: 100px; 
  max-width: 800px;
  width: 100%;
  padding: 20px;
  box-sizing: border-box;
  text-shadow: 2px 2px 4px #000; 
}

.planetas-list {
  list-style-type: none;
  padding: 0;
}

.planetas-list li {
  background-color: rgba(0, 0, 0, 0.5);
  margin: 10px 0;
  padding: 20px;
  border-radius: 8px;
  text-align: left;
  transition: transform 0.3s;
}

.planetas-list li:hover {
  transform: scale(1.05);
}

.user-info {
  background-color: rgba(0, 0, 0, 0.7);
  padding: 20px;
  border-radius: 8px;
  display: inline-block;
  box-shadow: 0 0 10px rgba(255, 255, 0, 0.5);
}

.user-info p {
  margin: 5px 0;
  font-size: 1.2em;
  color: #FFD700; 
}

.loading,
.error {
  font-size: 1.2em;
  margin-top: 20px;
  color: red; 
}

</style>