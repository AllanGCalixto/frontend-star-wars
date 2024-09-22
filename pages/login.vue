<template>
  <div class="star-wars-theme">
    <div class="login-page">
      <h1>Login</h1>
      <form @submit.prevent="handleLogin">
        <div class="form-group">
          <label for="email">Email:</label>
          <input v-model="email" type="email" id="email" placeholder="Digite seu email" required />
        </div>
        <div class="form-group">
          <label for="password">Senha:</label>
          <input v-model="password" type="password" id="password" placeholder="Digite sua senha" required />
        </div>
        <button type="submit">Entrar</button>
      </form>
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

      <p>Não tem uma conta? <button @click="goToRegister">Registre-se aqui</button></p>

      <div class="google-login">
        <button @click="handleGoogleLogin">Entrar com Google</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter, useCookie } from '#imports' // ou '#app'

const email = ref('')
const password = ref('')
const errorMessage = ref('')

const router = useRouter()
const tokenCookie = useCookie('auth_token')

async function handleLogin() {
  try {
    const response = await $fetch('http://localhost:3000/auth/login', {
      method: 'POST',
      body: { email: email.value, password: password.value },
    })
    if (response.access_token) {
      tokenCookie.value = response.access_token
      router.push('/')
    } else {
      errorMessage.value = 'Credenciais inválidas.'
    }
  } catch (error) {
    errorMessage.value = 'Ocorreu um erro ao fazer login.'
  }
}

function goToRegister() {
  router.push('/register')
}

// Função para iniciar o login com Google
function handleGoogleLogin() {
  window.location.href = 'http://localhost:3000/auth/google'
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
  align-items: center;
  justify-content: center;
  font-family: 'Star Jedi', sans-serif; 
  position: relative;
  overflow: hidden;
}

.login-page {
  background-color: rgba(0, 0, 0, 0.6); 
  padding: 40px;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(255, 255, 0, 0.5);
  text-align: center;
  width: 100%;
  max-width: 400px;
}

.login-page h1 {
  margin-bottom: 30px;
  font-size: 2.5em;
  text-shadow: 2px 2px 4px #000;
}

.form-group {
  margin-bottom: 20px;
  text-align: left;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  color: #fff;
  font-weight: bold;
}

.form-group input {
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 5px;
  background-color: rgba(255, 255, 0, 0.1);
  color: yellow;
  font-size: 1em;
}

.form-group input::placeholder {
  color: rgba(255, 255, 0, 0.7);
}

button[type="submit"] {
  width: 100%;
  padding: 12px;
  background-color: yellow;
  color: black;
  border: none;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s;
  font-size: 1.1em;
}

button[type="submit"]:hover {
  background-color: rgba(255, 255, 0, 0.8);
}

.error {
  color: red;
  margin-top: 15px;
  font-weight: bold;
}

.login-page p button {
  background: none;
  border: none;
  color: yellow;
  text-decoration: underline;
  cursor: pointer;
  padding: 0;
  font-size: 1em;
}

.login-page p button:hover {
  color: rgba(255, 255, 0, 0.8);
}

.google-login {
  margin-top: 20px;
}

.google-login button {
  width: 100%;
  padding: 12px;
  background-color: #4285F4; 
  color: white;
  border: none;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s;
  font-size: 1.1em;
}

.google-login button:hover {
  background-color: #357ae8;
}
</style>