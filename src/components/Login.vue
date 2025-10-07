<template>
    <div>
        <h2 class="text-2xl font-bold mb-4 text-center">Iniciar Sesión</h2>
        <form @submit.prevent="login">
            <div class="mb-4">
                <label class="block text-gray-700">Usuario</label>
                <input v-model="loginUsername" type="text" class="w-full p-2 border rounded" placeholder="Nombre de Usuario">
                <p v-if="loginError" class="text-red-500 text-sm">{{ loginError }}</p>
            </div>
            <div class="mb-4">
                <label class="block text-gray-700">Contraseña</label>
                <input v-model="loginPassword" type="password" class="w-full p-2 border rounded" placeholder="Contraseña">
            </div>
            <button type="submit" class="w-full bg-blue-500 text-white p-2 rounded 
            hover:bg-blue-600">Iniciar Sesión</button>
        </form>
        <p class="mt-4 text-center">
            ¿No tienes una cuenta? <a href="#" @click.prevent="$emit('show-register')"
            class="text-blue-500">Registrarse</a>
        </p>
    </div>
</template>

<script setup>
import { ref, defineEmits, toRef} from 'vue';

const emit = defineEmits(['login-success', 'show-register']);
const loginUsername = ref('');
const loginPassword = ref('');
const loginError = ref('');

const login = () => {
    loginError.value = '';
    const users = JSON.parse(localStorage.getItem('users') || '{}');
    if (users[loginUsername.value] && users[loginUsername.value] === loginPassword.value){
        emit('login-success', loginUsername.value);
        loginUsername.value = '';
        loginPassword.value = '';
    } else {
        loginError.value = 'Usuario o contraseña incorrectos';
    }
};
</script>