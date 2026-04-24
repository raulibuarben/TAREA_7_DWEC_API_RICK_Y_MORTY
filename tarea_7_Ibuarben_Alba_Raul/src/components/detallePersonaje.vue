<script setup>
// Recibimos el objeto personaje completo (formato JSON) 
defineProps(['personaje']);
</script>

<template>
  <div class="detalle-card" v-if="personaje">
    <img :src="personaje.image" :alt="personaje.name" />

    <div class="contenido">
      <h2>{{ personaje.name }}</h2> [cite: 20]
      
      <p><strong>Estado:</strong> {{ personaje.status }}</p>
      <p><strong>Especie:</strong> {{ personaje.species }}</p>
      <p><strong>Género:</strong> {{ personaje.gender }}</p>
      <p><strong>Origen:</strong> {{ personaje.origin.name }}</p> 
      <p><strong>Localización actual:</strong> {{ personaje.location.name }}</p>
      
      <p><strong>Apariciones en episodios:</strong> {{ personaje.episode.length }}</p>
    </div>
  </div>
</template>

<style scoped>
.detalle-card {
  background: #fff;
  border-radius: 15px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.2);
  display: flex;
  flex-wrap: wrap;
  max-width: 700px;
  margin: 20px auto;
  overflow: hidden;
  text-align: left;
}

img {
  width: 300px;
  height: 300px;
  object-fit: cover;
}

.contenido {
  padding: 30px;
  flex: 1;
}

h2 { margin-top: 0; color: #333; }
p { margin: 10px 0; font-size: 1.1rem; }
</style>