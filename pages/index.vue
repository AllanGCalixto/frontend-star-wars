<template>
  <div class="star-wars-theme">
    <!-- Navbar -->
    <nav class="navbar">
      <ul>
        <li><nuxt-link to="/planetas">Planetas</nuxt-link></li>
        <li><nuxt-link to="/filmes">Filmes</nuxt-link></li>
        <li><nuxt-link to="/usuarios">Usuários</nuxt-link></li>
        <li><a href="#" @click.prevent="handleLogout">Logout</a></li>
      </ul>
    </nav>

    <!-- Conteúdo da página -->
    <div class="content">
      <h1>Bem-vindo!</h1>
      <p>Selecione uma opção no menu acima.</p>

     
      <div v-if="user" class="user-info">
        <p><strong>Email do Usuário:</strong> {{ user.email }}</p>
        <p><strong>Nome do  Usuário:</strong> {{ user.firstName }}</p>
        <p><strong>Sobrenome do Usuário:</strong> {{ user.lastName }}</p>
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref, onMounted } from 'vue'
import { useCookie, useRouter } from '#imports'

const router = useRouter()
const tokenCookie = useCookie('auth_token')

const user = ref(null) 
const errorMessage = ref('') 
const isLoading = ref(true) 

function parseJwt(token) {
  try {
    const base64Url = token.split('.')[1]
    const base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/')
    const jsonPayload = decodeURIComponent(
      atob(base64)
        .split('')
        .map(function(c) {
          return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
        })
        .join('')
    )
    return JSON.parse(jsonPayload)
  } catch (error) {
    console.error('Erro ao decodificar o token:', error)
    return {}
  }
}

async function fetchUser(email) {
  if (!email) {
    errorMessage.value = 'Email não encontrado no token.'
    isLoading.value = false
    return
  }

  try {
    const response = await $fetch(`http://localhost:3000/user/${email}`, {
      method: 'GET',
      headers: {
        Authorization: `Bearer ${tokenCookie.value}`,
      },
    })
    user.value = response
  } catch (error) {
    errorMessage.value = error.message || 'Ocorreu um erro ao buscar os filmes.'
  } finally {
    isLoading.value = false
  }
}

function getEmailFromToken(token) {
  const decoded = parseJwt(token)
  return decoded.sub || ''
}

onMounted(() => {
  const token = tokenCookie.value
  if (!token) {
    router.push('/login')
  } else {
    fetchUser(getEmailFromToken(token))
    console.log('Email do usuário:', user.email) // Exemplo de uso
  }
})

function handleLogout() {
  tokenCookie.value = null
  router.push('/login')
}
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
}

.content h1 {
  font-size: 3em;
  margin-bottom: 20px;
  text-shadow: 2px 2px 4px #000;
}

.content p {
  font-size: 1.5em;
  margin-bottom: 40px;
  text-shadow: 1px 1px 2px #000;
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