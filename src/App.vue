<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

// const state = reactive({
//   guitarras: db
// })
// console.log(state.guitarras)

const guitarras = ref([]);
const carrito = ref([]);
const guitarraPrincipal = ref({});

watch(carrito, () => {
  guardaCarritoLocal();
}, 
{
  deep: true,
})

onMounted(() => {
  guitarras.value = db;
  guitarraPrincipal.value = db[2];
  console.count(guitarras.value);
  const carritoLocal = localStorage.getItem("carrito");
  if (carritoLocal) carrito.value = JSON.parse(carritoLocal);
});

const guardaCarritoLocal = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );
  console.log(guitarra);
  if (existeCarrito < 0) {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  } else {
    carrito.value[existeCarrito].cantidad++;
  }
};

const setCantidad = (guitarra, newCantidad) => {
  const indexGuitarra = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );
  if (newCantidad >= 1) carrito.value[indexGuitarra].cantidad = newCantidad;
};

const eliminarDeCarrito = (guitarra) => {
  const indexGuitarra = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );
  carrito.value.splice(indexGuitarra, 1);
};

const vaciarCarrito = () => {
  carrito.value = [];
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarraPrincipal="guitarraPrincipal"
    @set-cantidad="setCantidad"
    @eliminar-de-carrito="eliminarDeCarrito"
    @agregar-carrito="agregarCarrito"
    @vaciar-carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>

  <Footer />
</template>

<style scoped>
</style>
