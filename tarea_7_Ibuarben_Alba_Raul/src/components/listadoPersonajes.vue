<script setup>
// Recibimos la propiedad 'personajes' desde el padre (App.vue)
defineProps(['personajes']);

// Definimos los eventos que este componente puede enviar al padre
const emit = defineEmits(['seleccionar']);
</script>

<template>
  <div class="grid-personajes">
    <div 
      v-for="personaje in personajes" 
      :key="personaje.id" 
      class="tarjeta-clicable"
      @click="emit('seleccionar', personaje)"
    >
      <img :src="personaje.image" :alt="personaje.name" class="img-personaje" />
      
      <div class="info-basica">
        <h3>{{ personaje.name }}</h3>
        
        <p><strong>Estado:</strong> {{ personaje.status }}</p>
        <p><strong>Especie:</strong> {{ personaje.species }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.grid-personajes {
  display: grid;
  /* Creamos una cuadrícula responsiva */
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 20px;
  padding: 20px;
}

.tarjeta-clicable {
  border: 1px solid #e0e0e0;
  border-radius: 10px;
  overflow: hidden;
  background-color: #fff;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.tarjeta-clicable:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
}

.img-personaje {
  width: 100%;
  height: auto;
  display: block;
}

.info-basica {
  padding: 15px;
  text-align: left;
}

h3 {
  margin: 0 0 10px 0;
  color: #2c3e50;
}

p {
  margin: 5px 0;
  font-size: 0.9rem;
  color: #666;
}
</style>