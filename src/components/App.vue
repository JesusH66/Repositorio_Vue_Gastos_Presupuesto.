<template>
  <div class="max-w-md mx-auto bg-white p-6 rounded-lg shadow-md">
    <Register v-if="!isLoggedIn && showRegister" @register-success="showRegister = false" />
    <Login v-if="!isLoggedIn && !showRegister" @login-success="handleLogin" @show-register="showRegister = true" />
    <Calculator v-if="isLoggedIn" :current-user="currentUser" @logout="logout" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Register from './Register.vue';
import Login from './Login.vue';
import Calculator from './Calculator.vue';

const isLoggedIn = ref(false);
const showRegister = ref(false);
const currentUser = ref(null);

const handleLogin = (username) => {
  isLoggedIn.value = true;
  currentUser.value = username;
};

const logout = () => {
  isLoggedIn.value = false;
  currentUser.value = null;
};
</script>