<template>
  <div class="star-wars-theme">
    <!-- Conteúdo da página de registro -->
    <div class="register-page">
      <h1>Registro</h1>
      <form @submit.prevent="handleRegister">
        <div class="form-group">
          <label for="firstName">Nome:</label>
          <input v-model="firstName" type="text" id="firstName" placeholder="Digite seu nome" required />
        </div>
        <div class="form-group">
          <label for="lastName">Sobrenome:</label>
          <input v-model="lastName" type="text" id="lastName" placeholder="Digite seu sobrenome" required />
        </div>
        <div class="form-group">
          <label for="email">Email:</label>
          <input v-model="email" type="email" id="email" placeholder="Digite seu email" required />
        </div>
        <div class="form-group">
          <label for="password">Senha:</label>
          <input v-model="password" type="password" id="password" placeholder="Digite sua senha" required />
        </div>
        <div class="form-group">
          <label for="confirmPassword">Confirmar Senha:</label>
          <input v-model="confirmPassword" type="password" id="confirmPassword" placeholder="Confirme sua senha" required />
        </div>
        <button type="submit">Registrar</button>
      </form>
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
      <p v-if="successMessage" class="success">{{ successMessage }}</p>
      <p>Já tem uma conta? <button @click="goToLogin">Faça Login aqui</button></p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter, useCookie } from '#imports' // ou '#app'

const firstName = ref('')
const lastName = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const errorMessage = ref('')
const successMessage = ref('')

const router = useRouter()
const tokenCookie = useCookie('auth_token')

async function handleRegister() {
  if (password.value !== confirmPassword.value) {
    errorMessage.value = 'As senhas não coincidem.'
    successMessage.value = ''
    return
  }

  try {
    const response = await $fetch('http://localhost:3000/user', {
      method: 'POST',
      body: {
        firstName: firstName.value,
        lastName: lastName.value,
        email: email.value,
        password: password.value,
      },
    })

    if (response.firstName) {
      successMessage.value = 'Registro realizado com sucesso! Redirecionando para o login...'
      errorMessage.value = ''
      setTimeout(() => {
        router.push('/login')
      }, 2000)
    } else {
      errorMessage.value = response.message || 'Erro ao registrar.'
      successMessage.value = ''
    }
  } catch (error) {
    errorMessage.value = 'Ocorreu um erro ao registrar.'
    successMessage.value = ''
  }
}

function goToLogin() {
  router.push('/login')
}
</script>

<style scoped>

.star-wars-theme {
  background-image: url('/images/Starwars.webp'), 
                    radial-gradient(white 0.5px, transparent 0.5px),
                    radial-gradient(white 0.5px, transparent 0.5px);
  background-color: black; /* Fundo preto */
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


.register-page {
  background-color: rgba(0, 0, 0, 0.6); 
  padding: 40px;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(255, 255, 0, 0.5);
  text-align: center;
  width: 100%;
  max-width: 500px;
}

.register-page h1 {
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

.success {
  color: green;
  margin-top: 15px;
  font-weight: bold;
}

.register-page p button {
  background: none;
  border: none;
  color: yellow;
  text-decoration: underline;
  cursor: pointer;
  padding: 0;
  font-size: 1em;
}

.register-page p button:hover {
  color: rgba(255, 255, 0, 0.8);
}
</style>