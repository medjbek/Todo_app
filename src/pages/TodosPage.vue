<script setup>
import { ref, onMounted } from 'vue'
import { api } from 'boot/axios'

const todos = ref([])
const loading = ref(true)
const errorMessage = ref('')

const loadTodos = async () => {
  loading.value = true
  errorMessage.value = ''

  try {
    const response = await api.get('/todos')
    todos.value = response.data.todos
  } catch (error) {
    console.error('ERREUR CHARGEMENT TODOS', error)
    errorMessage.value = 'Impossible de charger les tâches.'
  } finally {
    loading.value = false
  }
}

const formatDate = (dateString) => {
  if (!dateString) return ''
  return new Date(dateString).toLocaleDateString('fr-FR')
}

onMounted(() => {
  loadTodos()
})
</script>

<template>
  <q-page class="todos-page q-pa-lg">
    <div class="todos-wrapper">
      <div class="page-header q-mb-xl">
        <div class="page-title">Mes tâches</div>
        <div class="page-subtitle">Organise tes actions pour une plus grande autonomie</div>
      </div>

      <div v-if="loading" class="loading-box">
        <q-spinner size="40px" color="primary" />
      </div>

      <div v-else-if="errorMessage" class="message-box error-box">
        {{ errorMessage }}
      </div>

      <div v-else-if="todos.length === 0" class="message-box">Aucune tâche pour le moment.</div>

      <div v-else class="todo-list">
        <q-card v-for="todo in todos" :key="todo.id" class="todo-card" flat>
          <q-card-section class="q-pa-lg">
            <div class="title-row">
              <div class="todo-check" :class="todo.is_completed ? 'done' : 'pending'"></div>

              <div class="todo-title">
                {{ todo.title }}
              </div>
            </div>

            <div v-if="todo.description" class="todo-description q-mt-sm">
              {{ todo.description }}
            </div>

            <div class="q-mt-md">
              <q-badge :class="todo.is_completed ? 'status-done' : 'status-progress'">
                {{ todo.is_completed ? '✔ Terminé' : '✖ En cours' }}
              </q-badge>
            </div>

            <div class="todo-date q-mt-sm">Créée le : {{ formatDate(todo.created_at) }}</div>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<style scoped>
.todos-page {
  min-height: 100vh;
  background: linear-gradient(180deg, #ede9ff 0%, #f4f1ff 100%);
}

.todos-wrapper {
  max-width: 720px;
  margin: 0 auto;
}

.page-header {
  text-align: center;
}

.page-title {
  font-size: 42px;
  font-weight: 800;
  color: #5b3cc4;
}

.page-subtitle {
  margin-top: 8px;
  color: #7a7194;
}

.todo-list {
  display: grid;
  gap: 18px;
}

.todo-card {
  background: #ffffff;
  border-radius: 20px;
  border: 1px solid #e9e2ff;
  box-shadow: 0 12px 28px rgba(91, 60, 196, 0.12);
}

.title-row {
  display: flex;
  align-items: center;
  gap: 10px;
}

.todo-check {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  flex-shrink: 0;
}

.todo-check.done {
  background: #1f8f47;
}

.todo-check.pending {
  border: 2px solid #6a4ae3;
  background: transparent;
}

.todo-title {
  font-size: 22px;
  font-weight: 700;
  color: #2e2547;
}

.todo-description {
  color: #6f6788;
}

.status-done {
  background: #e7f7ec;
  color: #1f8f47;
  padding: 6px 10px;
  border-radius: 999px;
}

.status-progress {
  background: #ede9ff;
  color: #5b3cc4;
  padding: 6px 10px;
  border-radius: 999px;
}

.todo-date {
  font-size: 13px;
  color: #948cab;
}

.loading-box {
  display: flex;
  justify-content: center;
  margin-top: 30px;
}

.message-box {
  background: #ffffff;
  border-radius: 16px;
  padding: 20px;
  text-align: center;
  border: 1px solid #e9e2ff;
}

.error-box {
  color: #c62828;
}
</style>
