<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { api } from 'boot/axios'

const router = useRouter()

const leftDrawerOpen = ref(false)

const isAuthenticated = ref(false)

const updateAuthState = () => {
  isAuthenticated.value = !!localStorage.getItem('token')
}

const toggleLeftDrawer = () => {
  updateAuthState()
  leftDrawerOpen.value = !leftDrawerOpen.value
}

const goToTodos = () => {
  leftDrawerOpen.value = false
  router.push('/todos')
}

const goToLogin = () => {
  leftDrawerOpen.value = false
  router.push('/login')
}

const logout = async () => {
  try {
    await api.post('/logout')
  } catch (error) {
    console.error('ERREUR LOGOUT', error)
  } finally {
    localStorage.removeItem('token')
    updateAuthState()
    leftDrawerOpen.value = false
    router.push('/login')
  }
}

onMounted(() => {
  updateAuthState()
})
</script>

<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="bg-violet text-white">
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" />

        <q-toolbar-title>Appli Todo List</q-toolbar-title>

        <div class="header-right">Sesame Corp</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" side="left" overlay bordered class="drawer-menu">
      <q-list>
        <q-item-label header class="drawer-title"> Navigation </q-item-label>

        <q-item clickable v-ripple @click="goToTodos">
          <q-item-section avatar>
            <q-icon name="checklist" />
          </q-item-section>

          <q-item-section> Mes tâches </q-item-section>
        </q-item>

        <q-item v-if="!isAuthenticated" clickable v-ripple @click="goToLogin">
          <q-item-section avatar>
            <q-icon name="login" />
          </q-item-section>

          <q-item-section> Se connecter </q-item-section>
        </q-item>

        <q-item v-if="isAuthenticated" clickable v-ripple @click="logout">
          <q-item-section avatar>
            <q-icon name="logout" />
          </q-item-section>

          <q-item-section> Se déconnecter </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<style scoped>
.bg-violet {
  background-color: #5b3cc4;
}

.header-right {
  font-size: 14px;
  font-weight: 500;
}

.drawer-menu {
  background: #f8f5ff;
}

.drawer-title {
  color: #5b3cc4;
  font-weight: 700;
}
</style>
