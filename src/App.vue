<template>
  <div class="contenedor">
    <h1>🔍 Búsqueda de Autos</h1>

    <!--------------------- Buscar por ID --------------------->
    <section class="busqueda">
      <h2>Buscar por ID</h2>
      <input v-model="id" type="number" placeholder="ID del auto" />
      <button @click="buscarPorId">Buscar</button>

      <div v-if="loadingId">Buscando auto...</div>

      <div v-if="autoPorId" class="tarjeta-auto">
        <p><strong>Marca:</strong> {{ autoPorId.marca }}</p>
        <p><strong>Modelo:</strong> {{ autoPorId.modelo }}</p>
      </div>

      <div v-if="errorId" class="error">{{ errorId }}</div>
    </section>

    <!--------------------- Buscar por Marca y Modelo --------------------->
    <section class="busqueda">
      <h2>Buscar por Marca y Modelo</h2>
      <input v-model="marca" type="text" placeholder="Marca (ej. Volkswagen)" />
      <input v-model="modelo" type="text" placeholder="Modelo (ej. Beetle)" />
      <button @click="buscarPorMarcaModelo">Buscar</button>

      <div v-if="loadingMarcaModelo">Buscando autos...</div>

      <div v-if="autosPorMarcaModelo.length">
        <h3>Resultados encontrados:</h3>
        <div v-for="(auto, index) in autosPorMarcaModelo" :key="index" class="tarjeta-auto">
          <p><strong>Año:</strong> {{ auto.anio }}</p>
          <p><strong>Precio:</strong>  ${{ parseFloat(auto.precio).toLocaleString() }}</p>
          <p><strong>Potencia:</strong>  {{ auto.potencia_hp }} HP</p>
          <p><strong>Seguridad:</strong> ⭐ {{ auto.seguridad }} / 5</p>
        </div>
      </div>

      <div v-if="errorMarcaModelo" class="error">{{ errorMarcaModelo }}</div>
    </section>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'


// Estados
const id = ref('')
const marca = ref('')
const modelo = ref('')

const autoPorId = ref(null)
const errorId = ref('')
const loadingId = ref(false)

const autosPorMarcaModelo = ref([])
const errorMarcaModelo = ref('')
const loadingMarcaModelo = ref(false)


// Buscar por ID
const buscarPorId = async () => {
  autoPorId.value = null
  errorId.value = ''
  loadingId.value = true

  try {
    const res = await axios.get(`https://autoperfectolaravel.onrender.com/api/autos/${id.value}`)
    autoPorId.value = res.data
  } catch (err) {
    errorId.value = 'Auto no encontrado.'
  } finally {
    loadingId.value = false
  }
}

// Buscar por Marca y Modelo
const buscarPorMarcaModelo = async () => {
  autosPorMarcaModelo.value = []
  errorMarcaModelo.value = ''
  loadingMarcaModelo.value = true

  try {
    const res = await axios.get('https://autoperfectolaravel.onrender.com/api/autos/buscar', {
      params: {
        marca: marca.value,
        modelo: modelo.value
      }
    })
    autosPorMarcaModelo.value = res.data
  } catch (err) {
    errorMarcaModelo.value = 'No se encontraron autos con esa marca y modelo.'
  } finally {
    loadingMarcaModelo.value = false
  }
}
</script>

<!--------------------- Estilos  --------------------->
<style scoped>
.contenedor {
  max-width: 720px;
  margin: 2rem auto;
  font-family: 'Segoe UI', sans-serif;
  color: #2c3e50;
}

.busqueda {
  background: #f7f7f7;
  padding: 1.5rem;
  margin-bottom: 2rem;
  border-radius: 10px;
  box-shadow: 0 0 6px rgba(0,0,0,0.1);
}

input {
  margin: 0.5rem 0;
  padding: 0.6rem;
  width: 100%;
  border: 1px solid #bbb;
  border-radius: 6px;
  font-size: 1rem;
  width: -webkit-fill-available;
}

button {
  padding: 0.6rem 1.2rem;
  margin-top: 0.5rem;
  background-color: #3498db;
  color: #fff;
  border: none;
  border-radius: 6px;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background-color: #2980b9;
}

.error {
  color: #e74c3c;
  margin-top: 1rem;
  font-weight: bold;
}

.tarjeta-auto {
  background-color: #fff;
  border-left: 5px solid #27ae60;
  padding: 1rem;
  margin-top: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.05);
}

.tarjeta-auto p {
  margin: 0.3rem 0;
  color: #34495e;
}
</style>
