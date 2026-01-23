<template>
  <v-container fluid class="fill-height login-container">
    <v-row align="center" justify="center">
      <v-col cols="12" sm="8" md="5" lg="4" xl="3">
        <v-card class="elevation-12 login-card" rounded="lg">
          <!-- Header Section -->
          <v-card-title class="text-center pa-8 pb-4">
            <div class="w-100">
              <v-avatar size="80" class="mb-4 elevation-3" color="primary">
                <v-icon size="50" color="white">mdi-account-circle</v-icon>
              </v-avatar>
              <h2 class="text-h4 font-weight-bold mb-2">Bienvenido</h2>
              <p class="text-subtitle-1 text-medium-emphasis">Inicia sesión en tu cuenta</p>
            </div>
          </v-card-title>

          <!-- Form Section -->
          <v-card-text class="px-8 pb-8">
            <v-form 
              ref="formRef"
              v-model="isFormValid" 
              validate-on="submit lazy" 
              @submit.prevent="submit"
            >
              <!-- Username Field -->
              <v-text-field
                v-model="userName"
                :rules="userNameRules"
                label="Nombre de usuario"
                placeholder="Ingresa tu usuario"
                prepend-inner-icon="mdi-account"
                variant="outlined"
                color="primary"
                class="mb-2"
                :disabled="loading"
                autocomplete="username"
                @keydown.enter="submit"
              ></v-text-field>

              <!-- Password Field -->
              <v-text-field
                v-model="password"
                :rules="passwordRules"
                :type="showPassword ? 'text' : 'password'"
                label="Contraseña"
                placeholder="Ingresa tu contraseña"
                prepend-inner-icon="mdi-lock"
                :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
                @click:append-inner="showPassword = !showPassword"
                variant="outlined"
                color="primary"
                class="mb-2"
                :disabled="loading"
                autocomplete="current-password"
                @keydown.enter="submit"
              ></v-text-field>

              <!-- Remember Me & Forgot Password -->
              <div class="d-flex justify-space-between align-center mb-4">
                <v-checkbox
                  v-model="rememberMe"
                  label="Recordarme"
                  color="primary"
                  hide-details
                  density="compact"
                  :disabled="loading"
                ></v-checkbox>
                <v-btn
                  variant="text"
                  color="primary"
                  size="small"
                  :disabled="loading"
                  @click="handleForgotPassword"
                >
                  ¿Olvidaste tu contraseña?
                </v-btn>
              </div>

              <!-- Submit Button -->
              <v-btn
                :loading="loading"
                :disabled="loading"
                type="submit"
                color="primary"
                size="large"
                block
                class="mb-4 text-none font-weight-bold"
                elevation="2"
              >
                <v-icon start>mdi-login</v-icon>
                Iniciar Sesión
              </v-btn>

              <!-- Divider -->
              <v-divider class="my-4"></v-divider>

              <!-- Social Login Options -->
              <div class="text-center">
                <p class="text-caption text-medium-emphasis mb-3">O continúa con</p>
                <div class="d-flex justify-center gap-2">
                  <v-btn
                    variant="outlined"
                    color="primary"
                    size="large"
                    :disabled="loading"
                    @click="handleSocialLogin('google')"
                  >
                    <v-icon>mdi-google</v-icon>
                  </v-btn>
                  <v-btn
                    variant="outlined"
                    color="primary"
                    size="large"
                    :disabled="loading"
                    @click="handleSocialLogin('github')"
                  >
                    <v-icon>mdi-github</v-icon>
                  </v-btn>
                  <v-btn
                    variant="outlined"
                    color="primary"
                    size="large"
                    :disabled="loading"
                    @click="handleSocialLogin('microsoft')"
                  >
                    <v-icon>mdi-microsoft</v-icon>
                  </v-btn>
                </div>
              </div>
            </v-form>
          </v-card-text>

          <!-- Footer -->
          <v-card-actions class="px-8 pb-6 pt-0">
            <v-spacer></v-spacer>
            <span class="text-caption text-medium-emphasis">
              ¿No tienes cuenta?
              <v-btn
                variant="text"
                color="primary"
                size="small"
                class="text-none"
                :disabled="loading"
                @click="handleRegister"
              >
                Regístrate
              </v-btn>
            </span>
            <v-spacer></v-spacer>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Snackbar for notifications -->
    <v-snackbar
      v-model="snackbar.show"
      :color="snackbar.color"
      :timeout="3000"
      location="top"
    >
      {{ snackbar.message }}
      <template v-slot:actions>
        <v-btn
          variant="text"
          @click="snackbar.show = false"
        >
          Cerrar
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script setup>
import { ref, reactive } from 'vue'

// Form state
const formRef = ref(null)
const isFormValid = ref(false)
const loading = ref(false)
const userName = ref('')
const password = ref('')
const showPassword = ref(false)
const rememberMe = ref(false)

// Snackbar state
const snackbar = reactive({
  show: false,
  message: '',
  color: 'success'
})

// Validation rules
const userNameRules = [
  value => !!value || 'El nombre de usuario es requerido',
  value => (value && value.length >= 3) || 'Mínimo 3 caracteres',
  value => checkApi(value)
]

const passwordRules = [
  value => !!value || 'La contraseña es requerida',
  value => (value && value.length >= 6) || 'Mínimo 6 caracteres'
]

// API check with debounce
let timeout = -1
async function checkApi(userName) {
  return new Promise(resolve => {
    clearTimeout(timeout)

    if (!userName) return resolve(true)

    timeout = setTimeout(() => {
      // Simulación de verificación de API
      if (userName === 'johnleider') {
        return resolve('Este usuario ya está en uso')
      }
      return resolve(true)
    }, 800)
  })
}

// Submit handler
async function submit() {
  const { valid } = await formRef.value.validate()
  
  if (!valid) return

  loading.value = true

  try {
    // Simulación de llamada API
    await new Promise(resolve => setTimeout(resolve, 1500))

    // Aquí iría tu lógica de autenticación real
    console.log('Login attempt:', {
      userName: userName.value,
      password: password.value,
      rememberMe: rememberMe.value
    })

    showSnackbar('¡Inicio de sesión exitoso!', 'success')
    
    // Simular redirección
    setTimeout(() => {
      // router.push('/dashboard')
      console.log('Redirecting to dashboard...')
    }, 1000)

  } catch (error) {
    showSnackbar('Error al iniciar sesión. Intenta nuevamente.', 'error')
    console.error('Login error:', error)
  } finally {
    loading.value = false
  }
}

// Helper functions
function showSnackbar(message, color = 'success') {
  snackbar.message = message
  snackbar.color = color
  snackbar.show = true
}

function handleForgotPassword() {
  showSnackbar('Funcionalidad de recuperación próximamente', 'info')
  // router.push('/forgot-password')
}

function handleRegister() {
  showSnackbar('Redirigiendo a registro...', 'info')
  // router.push('/register')
}

function handleSocialLogin(provider) {
  showSnackbar(`Iniciando sesión con ${provider}...`, 'info')
  // Implementar lógica de OAuth
}
</script>

<style scoped>
.login-container {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

.login-card {
  backdrop-filter: blur(10px);
  background-color: rgba(255, 255, 255, 0.95) !important;
}

.gap-2 {
  gap: 0.5rem;
}

/* Dark mode support */
.v-theme--dark .login-container {
  background: linear-gradient(135deg, #1e3a8a 0%, #4c1d95 100%);
}

.v-theme--dark .login-card {
  background-color: rgba(30, 30, 30, 0.95) !important;
}

/* Animations */
.login-card {
  animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
}

::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>