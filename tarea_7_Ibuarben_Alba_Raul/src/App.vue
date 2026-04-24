<script setup>
  import {ref, onMounted} from 'vue';
  //Importamos los componentes
  import buscador from './components/buscador.vue'
  import filtros from './components/filtros.vue';
  import detallePersonaje from './components/detallePersonaje.vue';
  import listadoPersonajes from './components/listadoPersonajes.vue';

  // Estados de la aplicación
  const personajes = ref([]);
  const cargando = ref (false);
  const error = ref(null);

  //Comunicación asíncrona
  const obtenerPersonajes = async (nombre = '', estado = '', especie = '') => {
    cargando.value = true;
    error.value = null;

    try {
      const respuesta = await fetch(`https://rickandmortyapi.com/api/character/?name=${nombre}&status=${estado}&species=${especie}`);
      
      if (!respuesta.ok) {
        if (respuesta.status === 404) throw new Error("Sin resultados"); // Estado sin resultados
        throw new Error("Fallo al conectar con la API"); // Estado de error 
      }

      const datos = await respuesta.json(); // Uso de response.json()
      
      personajes.value = datos.results; 
      
    } catch (err) {
      error.value = err.message;
      personajes.value = [];
    } finally {
      cargando.value = false; // Gestión del estado de carga [cite: 28]
    }
  };
  // Llamada inicial para mostrar datos al empezar
  onMounted(() => obtenerPersonajes());

  // Función para manejar la selección de un personaje para el detalle
  const verDetalle = (p) => {
    personajeSeleccionado.value = p;
  };
</script>
<template>
  <div class="container">
    <h1>Rick & Morty API</h1>

    <div class="herramientas" v-if="!personajeSeleccionado">
      <buscador @buscar="obtenerPersonajes" />
      <filtros @filtrar="obtenerPersonajes" />
    </div>

    <div v-if="cargando" class="mensaje">Cargando datos de la galaxia...</div>
    
    <div v-else-if="error" class="mensaje error">{{ error }}</div>

    <div v-else>
      <div v-if="personajeSeleccionado">
        <button @click="personajeSeleccionado = null">Volver al listado</button>
        <detallePersonaje :personaje="personajeSeleccionado" />
      </div>

      <listadoPersonajes 
        v-else 
        :personajes="personajes" 
        @seleccionar="verDetalle" 
      />
    </div>
  </div>
</template>

<style scoped>
/* Estilos básicos */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  text-align: center;
}

.herramientas {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin-bottom: 30px;
}

.mensaje {
  font-size: 1.2rem;
  margin-top: 50px;
}

.error {
  color: #ff4757;
}

button {
  margin-bottom: 20px;
  padding: 10px 20px;
  cursor: pointer;
}
</style>

