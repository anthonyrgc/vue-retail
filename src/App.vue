<template>
  <div class="container mx-auto p-4">
    <h1 class="text-3xl font-bold mb-4">Retail Dashboard</h1>

    <!-- Filters -->
    <div class="mb-6 flex flex-wrap gap-4">
      <select v-model="categoryFilter" class="border p-2 rounded">
        <option value="">All Categories</option>
        <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
      </select>

      <input
        type="number"
        v-model.number="minPriceFilter"
        placeholder="Min Price USD"
        class="border p-2 rounded w-32"
      />
      <input
        type="number"
        v-model.number="maxPriceFilter"
        placeholder="Max Price USD"
        class="border p-2 rounded w-32"
      />
    </div>

    <!-- Statistics -->
    <ProductStatistics :products="filteredProducts" />

    <!-- Product Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
      <ProductCard
        v-for="product in paginatedProducts"
        :key="product.id"
        :product="product"
        :exchangeRate="exchangeRate"
      />
    </div>

    <!-- Pagination -->
    <ProductPagination
      :totalItems="filteredProducts.length"
      :itemsPerPage="itemsPerPage"
      :currentPage="currentPage"
      @update:currentPage="(page) => (currentPage = page)"
    />

    <!-- Loading and error states -->
    <div v-if="loading" class="mt-4 text-center">Loading products...</div>
    <div v-if="error" class="mt-4 text-center text-red-600">{{ error }}</div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

import ProductCard from './components/ProductCard.vue'
import ProductPagination from './components/ProductPagination.vue'
import ProductStatistics from './components/ProductStatistics.vue'

// State variables
const products = ref([])
const categories = ref([])
const exchangeRate = ref(0)

const categoryFilter = ref('')
const minPriceFilter = ref(0)
const maxPriceFilter = ref(1000)

const currentPage = ref(1)
const itemsPerPage = 5

const loading = ref(false)
const error = ref('')

// Fetch data on mount
onMounted(async () => {
  loading.value = true
  try {
    const [productsRes, categoriesRes, exchangeRes] = await Promise.all([
      axios.get('https://fakestoreapi.com/products'),
      axios.get('https://fakestoreapi.com/products/categories'),
      axios.get('https://pydolarve.org/api/v1/dollar?page=bcv'),
    ])

    products.value = productsRes.data
    categories.value = categoriesRes.data
    // Aquí accedemos al price de banesco
    exchangeRate.value = exchangeRes.data.monitors.banesco.price
  } catch (e) {
    error.value = 'Failed to load data. Please try again later.'
  } finally {
    loading.value = false
  }
})

// Computed filtered products
const filteredProducts = computed(() => {
  return products.value.filter((p) => {
    const matchCategory = categoryFilter.value ? p.category === categoryFilter.value : true
    const matchPrice = p.price >= minPriceFilter.value && p.price <= maxPriceFilter.value
    return matchCategory && matchPrice
  })
})

// Paginate filtered products
const paginatedProducts = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage
  return filteredProducts.value.slice(start, start + itemsPerPage)
})
</script>
