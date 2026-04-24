<script setup>
// Definimos el evento para avisar a App.vue cuando cambie un filtro
const emit = defineEmits(['filtrar']);

// Variables para guardar los valores seleccionados
import { ref } from 'vue';
const estado = ref('');
const especie = ref('');

// Función que se ejecuta cada vez que cambia un selector
const enviarFiltros = () => {
  // Emitimos un objeto con ambos valores hacia el padre (App.vue)
  emit('filtrar', { estado: estado.value, especie: especie.value });
};
</script>

<template>
  <div class="filtros-container">
    <select v-model="estado" @change="enviarFiltros">
      <option value="">Cualquier estado</option>
      <option value="alive">Vivo</option>
      <option value="dead">Muerto</option>
      <option value="unknown">Desconocido</option>
    </select>

    <select v-model="especie" @change="enviarFiltros">
      <option value="">Cualquier especie</option>
      <option value="human">Humano</option>
      <option value="alien">Alienígena</option>
      <option value="humanoid">Humanoide</option>
    </select>
  </div>
</template>

<style scoped>
.filtros-container {
  display: flex;
  gap: 15px;
  justify-content: center;
  margin: 10px 0;
}
select {
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #ccc;
  background-color: white;
  cursor: pointer;
}
</style>