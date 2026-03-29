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
    console.log('TODOS CHARGÉS', response.data.todos)
  } catch (error) {
    console.error('ERREUR CHARGEMENT TODOS', error)
    errorMessage.value = 'Impossible de charger les tâches.'
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  loadTodos()
})

const formatDate = (dateString) => {
  if (!dateString) {
    return ''
  }

  return new Date(dateString).toLocaleDateString('fr-FR')
}
</script>

<template>
  <q-page class="q-pa-md">
    <div class="text-h4 q-mb-md">Mes tâches</div>

    <div v-if="loading" class="q-mt-lg">
      <q-spinner size="40px" color="primary" />
    </div>

    <div v-else-if="errorMessage" class="text-negative q-mt-md">
      {{ errorMessage }}
    </div>

    <div v-else-if="todos.length === 0" class="text-grey-7 q-mt-md">
      Aucune tâche pour le moment.
    </div>

    <div v-else class="q-gutter-md">
      <q-card v-for="todo in todos" :key="todo.id" class="todo-card" flat bordered>
        <q-card-section>
          <div class="row items-start justify-between">
            <div>
              <div class="text-h6">
                {{ todo.title }}
              </div>

              <div v-if="todo.description" class="text-grey-7 q-mt-sm">
                {{ todo.description }}
              </div>

              <div class="q-mt-md">
                <q-badge :color="todo.is_completed ? 'positive' : 'grey'" text-color="white">
                  {{ todo.is_completed ? 'Terminé' : 'En cours' }}
                </q-badge>
              </div>

              <div class="text-caption text-grey q-mt-sm">
                Créée le : {{ formatDate(todo.created_at) }}
              </div>
            </div>
          </div>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<style scoped>
.todo-card {
  border-radius: 16px;
}
</style>
