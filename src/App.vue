<script setup>
import { ref, reactive, onMounted, watch } from 'vue';

import { uid } from 'uid';

import Header from './components/Header.vue';
import Form from './components/Form.vue';
import Paciente from './components/Paciente.vue';

const pacientes = ref([]);

const paciente = reactive({
  id: null,
  nombre: '',
  propietario: '',
  email: '',
  alta: '',
  sintomas: '',
});

onMounted(() => {
  const pacientesLocalStorage = localStorage.getItem('pacientes');

  if (pacientesLocalStorage) {
    pacientes.value = JSON.parse(pacientesLocalStorage);
  }
});

const guardarLocalStorage = () => {
  localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
}

watch(pacientes, () => {
  guardarLocalStorage();
}, { deep: true });


const guardarPaciente = () => {

  if (paciente.id) {
    const { id } = paciente;

    const index = pacientes.value.findIndex(item => item.id === id);

    pacientes.value[index] = { ...paciente }

  } else {
    pacientes.value.push({
      ...paciente,
      id: uid(),
    });
  }

  Object.assign(paciente, {
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas: '',
    id: null,
  });
};

const actualizarPaciente = (id) => {
  const pacienteEditar = pacientes.value.find(item => item.id == id);

  Object.assign(paciente, pacienteEditar);
};

const eliminarPaciente = (id) => {
  pacientes.value = pacientes.value.filter(item => item.id !== id);
};
</script>

<template>
  <div class="container mx-auto mt-20">
    <Header />

    <div class="mt-12 md:flex">
      <Form v-model:nombre="paciente.nombre" v-model:propietario="paciente.propietario" v-model:email="paciente.email"
        v-model:alta="paciente.alta" v-model:sintomas="paciente.sintomas" @guardar-paciente="guardarPaciente"
        :id="paciente.id" />

      <div class="md:w-1/2">
        <h3 class="font-black text-3xl text-center">Administra Tus Pacientes</h3>
        <p class="text-lg mt-5 text-center mb-10">Información De <span
            class="text-indigo-600 font-bold">Pacientes</span></p>
        <div class="md:h-screen overflow-y-scroll" v-if="pacientes.length > 0">

          <Paciente v-for="paciente in pacientes" :paciente="paciente" @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente" />
        </div>
        <p v-else class="mt-20 text-2xl text-center">No Hay Pacientes</p>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
