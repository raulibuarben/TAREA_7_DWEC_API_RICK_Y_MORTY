<script setup>
  import { ref, onMounted } from 'vue';
  import buscador from './components/buscador.vue'
  import filtros from './components/filtros.vue';
  import detallePersonaje from './components/detallePersonaje.vue';
  import listadoPersonajes from './components/listadoPersonajes.vue';

  const personajes = ref([]);
  const cargando = ref(false);
  const error = ref(null);
  const filtroNombre = ref('');
  const filtroEstado = ref('');
  const filtroEspecie = ref('');
  const personajeSeleccionado = ref(null);

  // Variables para paginación
  const paginaActual = ref(1);
  const totalPaginas = ref(0);
  const haySiguiente = ref(false);
  const hayAnterior = ref(false);

  // CORRECCIÓN: Añadimos 'pagina' como parámetro con valor por defecto
  const obtenerPersonajes = async (nombre = filtroNombre.value, estado = filtroEstado.value, especie = filtroEspecie.value, pagina = paginaActual.value) => {
    
    filtroNombre.value = nombre;
    filtroEstado.value = estado;
    filtroEspecie.value = especie;
    paginaActual.value = pagina; // Ahora 'pagina' sí existe porque viene del parámetro

    cargando.value = true;
    error.value = null;

    try {
      const url = `https://rickandmortyapi.com/api/character/?page=${pagina}&name=${nombre}&status=${estado}&species=${especie}`;
      const respuesta = await fetch(url);
      
      if (!respuesta.ok) {
        if (respuesta.status === 404) throw new Error("Sin resultados");
        throw new Error("Fallo en la API");
      }

      const datos = await respuesta.json();
      personajes.value = datos.results;

      totalPaginas.value = datos.info.pages;
      haySiguiente.value = !!datos.info.next;
      hayAnterior.value = !!datos.info.prev; 

    } catch (err) {
      error.value = err.message;
      personajes.value = [];
    } finally {
      cargando.value = false;
    }
  };

  onMounted(() => obtenerPersonajes());

  const verDetalle = (p) => {
    personajeSeleccionado.value = p;
  };
</script>

<template>
  <div class="container">
    <h1>Rick & Morty API</h1>

    <div class="herramientas" v-if="!personajeSeleccionado">
      <buscador @buscar="(val) => { paginaActual = 1; obtenerPersonajes(val, filtroEstado, filtroEspecie, 1) }" />
      <filtros @filtrar="(f) => { paginaActual = 1; obtenerPersonajes(filtroNombre, f.estado, f.especie, 1) }" />
    </div>

    <div v-if="cargando" class="mensaje">Cargando datos de la galaxia...</div>
    <div v-else-if="error" class="mensaje error">{{ error }}</div>

    <div v-else>
      <div v-if="personajeSeleccionado">
        <button @click="personajeSeleccionado = null">Volver al listado</button>
        <detallePersonaje :personaje="personajeSeleccionado" />
      </div>

      <div v-else>
        <listadoPersonajes :personajes="personajes" @seleccionar="verDetalle" />
        
        <div class="paginacion" v-if="personajes.length > 0">
          <button 
            :disabled="!hayAnterior" 
            @click="obtenerPersonajes(filtroNombre, filtroEstado, filtroEspecie, paginaActual - 1)"
          >
            Anterior
          </button>

          <span>Página <strong>{{ paginaActual }}</strong> de {{ totalPaginas }}</span>

          <button 
            :disabled="!haySiguiente" 
            @click="obtenerPersonajes(filtroNombre, filtroEstado, filtroEspecie, paginaActual + 1)"
          >
            Siguiente
          </button>
        </div>
      </div>
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
.paginacion {
  margin-top: 30px;
  margin-bottom: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.paginacion button {
  padding: 8px 16px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.paginacion button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>

