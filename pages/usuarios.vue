<template>
  <div class="star-wars-theme">
    <!-- Navbar -->
    <nav class="navbar">
      <ul>
        <li><nuxt-link to="/">Home</nuxt-link></li>
        <li><nuxt-link to="/planetas">Planetas</nuxt-link></li>
        <li><nuxt-link to="/filmes">Filmes</nuxt-link></li>
        <li><a href="#" @click.prevent="handleLogout">Logout</a></li>
      </ul>
    </nav>

    <!-- Conteúdo da página de usuários -->
    <div class="usuarios-page">
      <h1>Usuários</h1>

      <!-- Estado de carregamento -->
      <div v-if="isLoading" class="loading">
        <p>Carregando usuários...</p>
      </div>

      <!-- Mensagem de erro -->
      <div v-else-if="errorMessage" class="error">
        <p>{{ errorMessage }}</p>
      </div>

      <!-- Lista de usuários -->
      <div v-else>
        <ul class="usuarios-list">
          <li v-for="usuario in usuarios" :key="usuario.id" class="usuario-item">
            <h2>{{ usuario.firstName }}</h2>
            <p><strong>Sobrenome: </strong> {{  usuario.lastName }}</p>
            <p><strong>Email:</strong> {{ usuario.email }}</p>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter, useCookie } from '#imports' // ou '#app'

const router = useRouter()
const tokenCookie = useCookie('auth_token')

const usuarios = ref([])
const isLoading = ref(true)
const errorMessage = ref('')

// Função para formatar a data
function formatDate(dateString) {
  const options = { year: 'numeric', month: 'long', day: 'numeric' }
  return new Date(dateString).toLocaleDateString('pt-BR', options)
}

// Função para fazer logout
function handleLogout() {
  tokenCookie.value = null
  router.push('/login')
}

// Função para buscar usuários
async function fetchUsuarios() {
  if (!tokenCookie.value) {
    router.push('/login')
    return
  }

  try {
    const response = await $fetch('http://localhost:3000/user', {
      method: 'GET',
      headers: {
        Authorization: `Bearer ${tokenCookie.value}`,
      },
    })
    usuarios.value = response
  } catch (error) {
    errorMessage.value = error.message || 'Ocorreu um erro ao buscar os usuários.'
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchUsuarios()
})
</script>

<style scoped>
/* Tema Star Wars */
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


.usuarios-page {
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

.usuarios-page h1 {
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

.usuarios-list {
  list-style-type: none;
  padding: 0;
}

.usuario-item {
  background-color: rgba(0, 0, 0, 0.5);
  margin: 15px 0;
  padding: 20px;
  border-radius: 8px;
  text-align: left;
  transition: transform 0.3s, background-color 0.3s;
}

.usuario-item:hover {
  transform: scale(1.02);
  background-color: rgba(255, 255, 0, 0.1);
}

.usuario-item h2 {
  margin-bottom: 10px;
  font-size: 1.8em;
  text-shadow: 1px 1px 2px #000;
}

.usuario-item p {
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