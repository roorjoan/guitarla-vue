<script setup>
import { ref, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(carrito, () => {
  guardarLocalStorage()
}, {
  deep: true
});

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];

  const carritoStorage = localStorage.getItem('carrito');
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex(element => element.id === guitarra.id);

  if (existeCarrito >= 0) {
    if (carrito.value[existeCarrito].cantidad >= 5) return;
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
}

const vaciarCarrito = () => carrito.value = [];

const decrementarCantidad = (id) => {
  const index = carrito.value.findIndex(element => element.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
}

const incrementarCantidad = (id) => {
  const index = carrito.value.findIndex(element => element.id === id);
  if (carrito.value[index].cantidad >= 5) return;
  carrito.value[index].cantidad++;
}

const eliminarGuitarra = (id) => {
  carrito.value = carrito.value.filter(guitarra => guitarra.id !== id);
}

const guardarLocalStorage = () => localStorage.setItem('carrito', JSON.stringify(carrito.value));

</script>

<template>
  <Header :carrito="carrito" :guitarra="guitarra" @vaciar-carrito="vaciarCarrito"
    @decrementar-cantidad="decrementarCantidad" @incrementar-cantidad="incrementarCantidad"
    @agregar-carrito="agregarCarrito" @eliminar-guitarra="eliminarGuitarra" />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra v-for="guitarra in guitarras" :key="guitarra.id" :guitarra="guitarra" @agregar-carrito="agregarCarrito" />
    </div>
  </main>

  <Footer />
</template>