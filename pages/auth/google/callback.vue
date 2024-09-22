<template>
  <div class="star-wars-theme">
    <!-- Página de processamento do callback -->
    <div class="callback-page">
      <p>Autenticando...</p>
    </div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'
import { useRouter, useCookie } from '#imports' // ou '#app'

const router = useRouter()
const tokenCookie = useCookie('auth_token')

// Função para extrair parâmetros da URL
function getQueryParam(param) {
  const urlParams = new URLSearchParams(window.location.search)
  return urlParams.get(param)
}

onMounted(() => {
  // Supondo que o backend redirecione com o token como parâmetro de query, por exemplo: ?token=XYZ
  const token = getQueryParam('token')

  if (token) {
    tokenCookie.value = token
    router.push('/')
  } else {
    // Se não houver token, redireciona para a página de login com uma mensagem de erro
    router.push('/login?error=Autenticação falhou. Por favor, tente novamente.')
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
  align-items: center;
  justify-content: center;
  font-family: 'Star Jedi', sans-serif; 
  position: relative;
  overflow: hidden;
}


.callback-page {
  background-color: rgba(0, 0, 0, 0.6); 
  padding: 40px;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(255, 255, 0, 0.5);
  text-align: center;
  color: yellow;
  font-size: 1.5em;
  text-shadow: 2px 2px 4px #000;
}
</style>