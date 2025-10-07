<template>
    <div>
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-2xl font-bold">Calculadora</h2>
        <button @click="$emit('logout')" class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600">Cerrar Sesión</button>
      </div>
  
      <!-- Establecer Presupuesto -->
      <div class="mb-6">
        <h3 class="text-lg font-semibold mb-2">Establecer Presupuesto</h3>
        <div class="flex space-x-2">
          <input v-model.number="presupuestoInicialInput" type="number" min="0" class="w-full p-2 border rounded" placeholder="Ingresa tu presupuesto">
          <button @click="setPresupuesto" class="bg-blue-500 text-white p-2 rounded hover:bg-blue-600">Establecer</button>
        </div>
      </div>
  
      <!-- Formulario de Nuevo Gasto -->
      <div class="mb-6">
        <h3 class="text-lg font-semibold mb-2">Agregar Gasto</h3>
        <div class="mb-4">
          <label class="block text-gray-700">Descripción</label>
          <input v-model="descripcionGasto" type="text" class="w-full p-2 border rounded" placeholder="Descripción del gasto">
          <p v-if="descripcionError" class="text-red-500 text-sm">{{ descripcionError }}</p>
        </div>
        <div class="mb-4">
          <label class="block text-gray-700">Monto</label>
          <input v-model.number="montoGasto" type="number" min="0" class="w-full p-2 border rounded" placeholder="Monto del gasto">
          <p v-if="montoError" class="text-red-500 text-sm">{{ montoError }}</p>
        </div>
        <button @click="addGasto" class="w-full bg-blue-500 text-white p-2 rounded hover:bg-blue-600">Agregar Gasto</button>
      </div>
  
      <!-- Lista de los Gastos -->
      <div class="mb-6">
        <h3 class="text-lg font-semibold mb-2">Lista de Gastos</h3>
        <ul class="divide-y divide-gray-200">
          <li v-for="(gasto, index) in gastos" :key="index" class="py-2 flex justify-between items-center">
            <span>{{ gasto.descripcion }}: ${{ gasto.monto }}</span>
            <button @click="removeGasto(index)" class="text-red-500 hover:text-red-700">Eliminar</button>
          </li>
        </ul>
      </div>
  
      <!-- Resumen del Presupuesto -->
      <div>
        <h3 class="text-lg font-semibold mb-2">Resumen</h3>
        <p><strong>Presupuesto Inicial:</strong> ${{ presupuestoInicial }}</p>
        <p><strong>Total Gastos:</strong> ${{ totalGastos }}</p>
        <p :class="presupuestoRestanteClass">
          <strong>Presupuesto Restante:</strong> ${{ presupuestoRestante }}
        </p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, watch, defineProps, defineEmits } from 'vue';
  
  const props = defineProps(['currentUser']);
  const emit = defineEmits(['logout']);
  
  const presupuestoInicial = ref(0);
  const presupuestoInicialInput = ref(null);
  const descripcionGasto = ref('');
  const montoGasto = ref(null);
  const gastos = ref([]);
  const descripcionError = ref('');
  const montoError = ref('');
  
  // Cargar datos del usuario actual desde localStorage
  const loadUserData = () => {
    if (props.currentUser) {
      const userData = JSON.parse(localStorage.getItem(`budget_${props.currentUser}`)) || {};
      presupuestoInicial.value = userData.presupuestoInicial || 0;
      gastos.value = userData.gastos || [];
    }
  };
  
  // Guardar datos del usuario en localStorage
  const saveUserData = () => {
    if (props.currentUser) {
      localStorage.setItem(`budget_${props.currentUser}`, JSON.stringify({
        presupuestoInicial: presupuestoInicial.value,
        gastos: gastos.value
      }));
    }
  };
  
  // Cargar datos al cambiar el usuario
  watch(() => props.currentUser, () => {
    loadUserData();
  }, { immediate: true });
  
  // Establecer presupuesto
  const setPresupuesto = () => {
    if (presupuestoInicialInput.value > 0) {
      presupuestoInicial.value = presupuestoInicialInput.value;
      presupuestoInicialInput.value = null;
      saveUserData();
    }
  };
  
  // Agregar gasto
  const addGasto = () => {
    descripcionError.value = '';
    montoError.value = '';
  
    if (!descripcionGasto.value.trim()) {
      descripcionError.value = 'La descripción no puede estar vacía';
      return;
    }
    if (!montoGasto.value || montoGasto.value <= 0) {
      montoError.value = 'El monto debe ser un número positivo mayor a cero';
      return;
    }
    if (!montoGasto.value > presupuestoRestante.value){
        montoError.value = 'El gasto es superior al presupuesto restante';
        return;
    }
  
    gastos.value.push({
      descripcion: descripcionGasto.value,
      monto: montoGasto.value
    });
    descripcionGasto.value = '';
    montoGasto.value = null;
    saveUserData();
  };
  
  // Eliminar gasto
  const removeGasto = (index) => {
    gastos.value.splice(index, 1);
    saveUserData();
  };
  
  // Propiedades computadas
  const totalGastos = computed(() => {
    return gastos.value.reduce((sum, gasto) => sum + gasto.monto, 0);
  });
  
  const presupuestoRestante = computed(() => {
    return presupuestoInicial.value - totalGastos.value;
  });
  
  const presupuestoRestanteClass = computed(() => {
    if (presupuestoRestante.value <= 0) {
      return 'text-danger';
    } else if (presupuestoRestante.value <= presupuestoInicial.value * 0.2) {
      return 'text-warning';
    } else {
      return 'text-success';
    }
  });
  </script>