<template>
  <div class="app">
    <h1>Tienda Alpha99 🏓</h1>
    <ProductList />
  </div>
</template>

<script setup>
import ProductList from './components/ProductList.vue'
</script>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #625e5e;
}
.app {
  text-align: center;
  padding: 2rem;
}
</style>