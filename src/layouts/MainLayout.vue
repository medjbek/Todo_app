<script setup></script>

<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="bg-violet text-white">
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" />

        <q-toolbar-title> Appli Todo List </q-toolbar-title>

        <div class="header-right">Sesame Corp</div>
      </q-toolbar>
    </q-header>

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
</style>
