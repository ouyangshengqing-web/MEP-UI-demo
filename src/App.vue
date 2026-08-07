<template>
  <div class="page-wrap">
    <LoginPage v-if="!isLoggedIn" @login-success="handleLoginSuccess" />
    <SchemeComparison v-else @logout="handleLogout" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import LoginPage from './views/LoginPage.vue'
import SchemeComparison from './components/SchemeComparison.vue'

const isLoggedIn = ref(false)

const handleLoginSuccess = () => {
  isLoggedIn.value = true
}

const handleLogout = () => {
  isLoggedIn.value = false
}
</script>
