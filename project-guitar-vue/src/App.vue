<script setup>
  import { ref, reactive, onMounted, watch } from 'vue'
  import { db } from './data/guitars.js';
  import Guitar from './components/Guitar.vue';
  import Header from './components/Header.vue';
  import Footer from './components/Footer.vue';

  const guitars = ref([]);
  const cart = ref([]);
  const guitar = ref({});

  // WATCH
  watch(cart, () => {
    guardarLocalStorage();
  }, 
  { 
    deep: true 
  });

  onMounted(() => {
    guitars.value = db;
    guitar.value = db[3];

    const cartLocalStorage = localStorage.getItem('cart');
    if (cartLocalStorage) {
      cart.value = JSON.parse(cartLocalStorage);
    }
  });

  //METODOS
  const addToCart = (guitar) => {
    const index = cart.value.findIndex(product => product.id === guitar.id);
    if (index >= 0) {
      cart.value[index].cantidad ++;
    } else {
      guitar.cantidad = 1;
      cart.value.push(guitar);
    }

    guardarLocalStorage();
  }

  const sumarCantidad = (id) => {
      const index = cart.value.findIndex(product => product.id === id);
      cart.value[index].cantidad ++;
    }

    const restarCantidad = (id) => {
      const index = cart.value.findIndex(product => product.id === id);
      if (cart.value[index].cantidad <= 1) {
        return;
      }
      cart.value[index].cantidad --;
    }

    const deleteProduct = (id) => {
      cart.value = cart.value.filter(product => product.id !== id);
    }

    const emptyCart = () => {
      cart.value = [];
    }

    // LOCAL STORAGE
    const guardarLocalStorage = () => {
      localStorage.setItem('cart', JSON.stringify(cart.value));
    }

</script>

<template>
    <Header 
      v-bind:cart="cart"
      v-bind:guitar="guitar"
      @sumar-cantidad="sumarCantidad"
      @restar-cantidad="restarCantidad"
      @add-to-cart="addToCart"
      @delete-product="deleteProduct"
      @empty-cart="emptyCart"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
          <Guitar v-for="guitar in guitars" v-bind:guitar="guitar" @add-to-cart="addToCart"/>
        </div>
    </main>

    <Footer />
</template>