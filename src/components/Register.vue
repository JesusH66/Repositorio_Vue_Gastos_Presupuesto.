<template>
    <div>
      <h2 class="text-2xl font-bold mb-4 text-center">Registro</h2>
      <form @submit.prevent="register">
        <div class="mb-4">
          <label class="block text-gray-700">Usuario</label>
          <input v-model="registerUsername" type="text" class="w-full p-2 border rounded" placeholder="Nombre de usuario">
          <p v-if="registerUsernameError" class="text-red-500 text-sm">{{ registerUsernameError }}</p>
        </div>
        <div class="mb-4">
          <label class="block text-gray-700">Contraseña</label>
          <input v-model="registerPassword" type="password" class="w-full p-2 border rounded" placeholder="Contraseña">
          <p v-if="registerPasswordError" class="text-red-500 text-sm">{{ registerPasswordError }}</p>
        </div>
        <button type="submit" class="w-full bg-blue-500 text-white p-2 rounded hover:bg-blue-600">Registrar</button>
      </form>
      <p class="mt-4 text-center">
        ¿Ya tienes cuenta? <a href="#" @click.prevent="$emit('show-register')" class="text-blue-500">Inicia sesión</a>
      </p>
    </div>
  </template>
  
  <script setup>
  import { ref, defineEmits } from 'vue';
  
  const emit = defineEmits(['register-success']);
  const registerUsername = ref('');
  const registerPassword = ref('');
  const registerUsernameError = ref('');
  const registerPasswordError = ref('');
  
  const register = () => {
    registerUsernameError.value = '';
    registerPasswordError.value = '';
  
    if (!registerUsername.value.trim()) {
      registerUsernameError.value = 'El nombre de usuario no puede estar vacío';
      return;
    }
    if (!registerPassword.value) {
      registerPasswordError.value = 'La contraseña no puede estar vacía';
      return;
    }
  
    const users = JSON.parse(localStorage.getItem('users') || '{}');
    if (users[registerUsername.value]) {
      registerUsernameError.value = 'El usuario ya existe';
      return;
    }
  
    users[registerUsername.value] = registerPassword.value;
    localStorage.setItem('users', JSON.stringify(users));
    alert('Registro exitoso. Por favor, inicia sesión.');
    emit('register-success');
    registerUsername.value = '';
    registerPassword.value = '';
  };
  </script>