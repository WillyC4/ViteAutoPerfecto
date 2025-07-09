<template>
  <div class="contenedor">
    <h1>Buscar auto por ID</h1>

    <div class="busqueda">
      <input
        v-model="id"
        type="number"
        placeholder="Ingrese el ID del auto"
        class="entrada"
      />
      <button @click="buscarAuto" class="boton">Buscar</button>
    </div>

    <div v-if="cargando" class="estado">Buscando auto...</div>

    <div v-if="auto" class="resultado">
      <h2>Resultado</h2>
      <p><strong>Marca:</strong> {{ auto.marca }}</p>
      <p><strong>Modelo:</strong> {{ auto.modelo }}</p>
      <p><strong>Precio:</strong> ${{ auto.precio.toLocaleString() }}</p>
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const id = ref('')
const auto = ref(null)
const error = ref('')
const cargando = ref(false)

const buscarAuto = async () => {
  auto.value = null
  error.value = ''
  cargando.value = true

  if (!id.value) {
    error.value = 'Por favor ingrese un ID válido.'
    cargando.value = false
    return
  }

  try {
    const respuesta = await axios.get(`https://autoperfectolaravel.onrender.com/api/autos/${id.value}`)
    auto.value = respuesta.data
  } catch (err) {
    error.value = 'Auto no encontrado o error en la búsqueda.'
  } finally {
    cargando.value = false
  }
}
</script>

<style scoped>
.contenedor {
  max-width: 600px;
  margin: 2rem auto;
  padding: 2rem;
  background: #f4f6f8;
  border-radius: 10px;
  box-shadow: 0 0 10px #ccc;
  font-family: 'Segoe UI', sans-serif;
}

h1 {
  text-align: center;
  margin-bottom: 1.5rem;
  color: #2c3e50;
}

h2 {
  color: black;
}

p {
  color: black;
}

.busqueda {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-bottom: 1rem;
}

.entrada {
  padding: 0.5rem;
  font-size: 1rem;
  width: 60%;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.boton {
  padding: 0.5rem 1rem;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.boton:hover {
  background-color: #2980b9;
}

.estado {
  text-align: center;
  color: #555;
  margin-top: 1rem;
}

.resultado {
  background: #fff;
  padding: 1rem;
  border-left: 4px solid #2ecc71;
  border-radius: 5px;
  margin-top: 1rem;
}

.error {
  color: #e74c3c;
  text-align: center;
  margin-top: 1rem;
}
</style>
