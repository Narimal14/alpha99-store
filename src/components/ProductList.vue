<template>
    <div class="product-list">
      <div v-for="product in products" :key="product.id" class="product-card">
        <img :src="selectedImages[product.id]" :alt="product.name" />
  
        <h2>{{ product.name }}</h2>
        <p>{{ product.description }}</p>
  
        <!-- Selector de color -->
        <label :for="'color-' + product.id">Color:</label>
        <select :id="'color-' + product.id" v-model="selectedColors[product.id]" @change="updateImage(product)">
          <option v-for="(color, index) in product.colors" :key="index" :value="color.name">
            {{ color.name }}
          </option>
        </select>
  
        <!-- Selector de cantidad -->
        <label :for="'quantity-' + product.id">Cantidad:</label>
        <input
          :id="'quantity-' + product.id"
          type="number"
          min="1"
          v-model.number="quantities[product.id]"
          @input="updateTotal(product)"
        />
  
        <!-- Mostrar total solo si está definido -->
        <strong v-if="totalPrices[product.id] !== undefined">
          Total: €{{ totalPrices[product.id].toFixed(2) }}
        </strong>
        <button @click="addToCart(product)" :disabled="!quantities[product.id]" class="add-to-cart-btn">Añadir al carrito</button>
      </div>
    </div>
  
    <!-- Mostrar carrito -->
    <div v-if="cart.length > 0" class="cart">
      <h3>Carrito de compras</h3>
      <ul>
        <li v-for="item in cart" :key="item.product.id">
          {{ item.product.name }} ({{ item.color }}) - €{{ item.totalPrice.toFixed(2) }} x {{ item.quantity }} 
          <button @click="removeFromCart(item)" class="remove-btn">Eliminar</button>
        </li>
      </ul>
      <strong>Total carrito: €{{ totalCart.toFixed(2) }}</strong>
    </div>
  </template>
  
  <script setup>
  import { reactive, onMounted, computed } from 'vue'
  import { products } from '../products.js'
  
  const selectedColors = reactive({})
  const selectedImages = reactive({})
  const quantities = reactive({})
  const totalPrices = reactive({})
  
  const cart = reactive([])
  
  // Inicializamos los productos y sus valores predeterminados
  onMounted(() => {
    products.forEach((product) => {
      if (product.colors && product.colors.length > 0) {
        selectedColors[product.id] = product.colors[0].name
        selectedImages[product.id] = product.colors[0].image
        quantities[product.id] = 1
        totalPrices[product.id] = product.price
      }
    })
  })
  
  // Cambia la imagen al seleccionar un color
  function updateImage(product) {
    const color = product.colors.find(c => c.name === selectedColors[product.id])
    selectedImages[product.id] = color.image
  }
  
  // Actualiza el precio total
  function updateTotal(product) {
    const qty = quantities[product.id]
    totalPrices[product.id] = qty * product.price
  }
  
  // Añadir al carrito
  function addToCart(product) {
    const productInCart = cart.find(item => item.product.id === product.id && item.color === selectedColors[product.id])
  
    if (productInCart) {
      productInCart.quantity += quantities[product.id]
      productInCart.totalPrice = productInCart.quantity * product.price
    } else {
      cart.push({
        product,
        color: selectedColors[product.id],
        quantity: quantities[product.id],
        totalPrice: quantities[product.id] * product.price
      })
    }
    quantities[product.id] = 1  // Reseteamos la cantidad
  }
  
  // Eliminar del carrito
  function removeFromCart(item) {
    const index = cart.indexOf(item)
    if (index > -1) {
      cart.splice(index, 1)
    }
  }
  
  // Total del carrito
  const totalCart = computed(() => {
    return cart.reduce((total, item) => total + item.totalPrice, 0)
  })
  </script>
  
  <style scoped>
  .product-list {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    padding: 2rem;
    gap: 2rem;
    background-color: #f5f5f5;
    min-height: 100vh;
  }
  
  .product-card {
    background: #ffffff;
    padding: 1rem;
    border-radius: 16px;
    width: 300px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
    text-align: center;
    transition: transform 0.2s ease;
  }
  
  .product-card:hover {
    transform: translateY(-4px);
  }
  
  img {
    width: 100%;
    height: 220px;
    object-fit: contain;
    border-radius: 12px;
    background-color: #f4f4f4;
  }
  
  h2 {
    font-size: 1.3rem;
    margin-top: 1rem;
    color: #333;
  }
  
  p {
    font-size: 1rem;
    color: #555;
    margin-top: 0.5rem;
  }
  
  label {
    display: block;
    margin: 1rem 0 0.5rem;
    font-weight: bold;
    color: #333;
  }
  
  select,
  input {
    padding: 0.5rem;
    width: 100%;
    margin-bottom: 1rem;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 1rem;
  }
  
  strong {
    display: block;
    margin: 1rem 0;
    font-size: 1.2rem;
    color: #0aa57e;
  }
  
  button {
    padding: 0.6rem 1.2rem;
    background-color: #ccc;
    color: black;
    border: none;
    border-radius: 8px;
    font-weight: bold;
    cursor: pointer;
    width: 100%;
    margin-top: 1rem;
  }
  
  .add-to-cart-btn {
    background-color: #0aa57e;
  }
  
  .cart {
    margin-top: 3rem;
    background-color: #8a7f7f;
    padding: 1rem;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  }
  
  .cart ul {
    list-style-type: none;
    padding: 0;
  }
  
  .cart ul li {
    margin-bottom: 1rem;
  }
  
  .cart button {
    background-color: #f44336;
    cursor: pointer;
    margin-top: 0.5rem;
  }
  
  strong {
    display: block;
    margin-top: 1rem;
    font-size: 1.2rem;
    color: #0aa57e;
  }
  </style>
  