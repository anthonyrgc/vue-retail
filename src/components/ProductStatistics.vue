<template>
  <div class="statistics-grid">
    <div class="stat-card">
      <h3 class="title">Total de productos</h3>
      <p class="value">{{ totalProducts }}</p>
    </div>
    <div class="stat-card">
      <h3 class="title">Categorías únicas</h3>
      <p class="value">{{ totalCategories }}</p>
    </div>
    <div class="stat-card">
      <h3 class="title">Precio promedio (USD)</h3>
      <p class="value">${{ averagePrice.toFixed(2) }}</p>
    </div>
  </div>
</template>

<script setup>
import { computed, toRef } from 'vue'

const props = defineProps({
  products: {
    type: Array,
    required: true,
  },
})

const products = toRef(props, 'products')

const totalProducts = computed(() => products.value.length)

const totalCategories = computed(() => {
  const categories = products.value.map((p) => p.category)
  return new Set(categories).size
})

const averagePrice = computed(() => {
  if (products.value.length === 0) return 0
  const total = products.value.reduce((sum, p) => sum + p.price, 0)
  return total / products.value.length
})
</script>

<style scoped>
.statistics-grid {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
  flex-wrap: wrap;
}

.stat-card {
  flex: 1 1 200px;
  background: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
  text-align: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.stat-card .title {
  font-size: 1rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: #555;
}

.stat-card .value {
  font-size: 2rem;
  font-weight: bold;
  color: #222;
}
</style>
