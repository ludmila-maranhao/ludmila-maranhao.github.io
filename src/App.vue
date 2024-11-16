<template>
  <div>
    <PaginaInicial v-if="currentPage === 'home'" @aviso="irParaAviso"/>
    <Aviso v-else-if="currentPage === 'aviso'" @iniciar="irParaFormulario"/>
    <Formulario v-else />
    <!-- <FormularioTeste v-else /> -->
  </div>
</template>

<script setup>
import { ref } from 'vue';
import PaginaInicial from './components/PaginaInicial.vue';
import Aviso from './components/Aviso.vue';
import Formulario from './components/Formulario.vue';
import FormularioTeste from './components/FormularioTeste.vue';

const currentPage = ref('home');

const irParaAviso = () => {
  currentPage.value = 'aviso';
};

const irParaFormulario = () => {
  currentPage.value = 'formulario';
};
</script>

<style scoped>

</style>