<script setup>
// Definimos el evento que enviará el nombre a App.vue
const emit = defineEmits(['buscar']);

// Función que se activa cada vez que el usuario escribe
const alEscribir = (evento) => {
  emit('buscar', evento.target.value);
};
</script>

<template>
  <div class="buscador-box">
    <input 
      type="text" 
      placeholder="Escribe un nombre (ej: Rick)..." 
      @input="alEscribir"
      class="input-busqueda"
    />
  </div>
</template>

<style scoped>
.input-busqueda {
  padding: 10px;
  width: 300px;
  border-radius: 5px;
  border: 1px solid #ccc;
}
</style>