<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { api } from 'boot/axios'

const router = useRouter()

const email = ref('')
const password = ref('')
const loading = ref(false)
const errorMessage = ref('')

const login = async () => {
  errorMessage.value = ''
  loading.value = true

  try {
    const response = await api.post('/login', {
      email: email.value,
      password: password.value,
    })

    const token = response.data.token
    localStorage.setItem('token', token)

    console.log('LOGIN OK', response.data)

    router.push('/todos')
  } catch (error) {
    console.error('ERREUR LOGIN', error)

    if (error.response && error.response.data && error.response.data.message) {
      errorMessage.value = error.response.data.message
    } else {
      errorMessage.value = 'Connexion impossible.'
    }
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <q-page class="row justify-center items-center q-pa-md">
    <q-card style="width: 100%; max-width: 420px; border-radius: 16px">
      <q-card-section>
        <div class="text-h4 text-center q-mb-sm">Connexion</div>
        <div class="text-subtitle2 text-grey-7 text-center">
          Connecte-toi pour accéder à tes tâches
        </div>
      </q-card-section>

      <q-card-section class="q-gutter-md">
        <q-input v-model="email" label="Email" type="email" outlined />

        <q-input v-model="password" label="Mot de passe" type="password" outlined />

        <div v-if="errorMessage" class="text-negative">
          {{ errorMessage }}
        </div>
      </q-card-section>

      <q-card-actions class="q-px-md q-pb-md">
        <q-btn
          label="Se connecter"
          color="primary"
          class="full-width"
          :loading="loading"
          @click="login"
        />
      </q-card-actions>
    </q-card>
  </q-page>
</template>
