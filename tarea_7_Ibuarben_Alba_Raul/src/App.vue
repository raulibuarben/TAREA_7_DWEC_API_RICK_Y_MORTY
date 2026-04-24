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
  const filtroNombre = ref('');
  const filtroEstado = ref('');
  const filtroEspecie = ref('');

  //Comunicación asíncrona
  const obtenerPersonajes = async (nombre = filtroNombre.value, estado = filtroEstado.value, especie = filtroEspecie.value) => {
    // Actualizamos los valores guardados
    filtroNombre.value = nombre;
    filtroEstado.value = estado;
    filtroEspecie.value = especie;

    cargando.value = true;
    error.value = null;

    try {
      // La URL ahora siempre lleva los 3 valores actualizados
      const url = `https://rickandmortyapi.com/api/character/?name=${filtroNombre.value}&status=${filtroEstado.value}&species=${filtroEspecie.value}`;
      const respuesta = await fetch(url);
      
      if (!respuesta.ok) {
        if (respuesta.status === 404) throw new Error("Sin resultados");
        throw new Error("Fallo en la API");
      }

      const datos = await respuesta.json();
      personajes.value = datos.results;
    } catch (err) {
      error.value = err.message;
      personajes.value = [];
    } finally {
      cargando.value = false;
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
      <buscador @buscar="(val) => obtenerPersonajes(val, filtroEstado, filtroEspecie)" />
      
      <filtros @filtrar="(f) => obtenerPersonajes(filtroNombre, f.estado, f.especie)" />
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

