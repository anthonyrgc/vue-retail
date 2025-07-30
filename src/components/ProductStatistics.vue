<template>
  <div class="statistics-card">
    <h2>Estadísticas</h2>
    <ul>
      <li>Total de productos: {{ totalProducts }}</li>
      <li>Categorías únicas: {{ totalCategories }}</li>
      <li>Precio promedio (USD): ${{ averagePrice.toFixed(2) }}</li>
    </ul>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { toRef } from 'vue'

const props = defineProps({
  products: {
    type: Array,
    required: true,
  },
})

// Asegúrate de que el prop esté reactivo
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
.statistics-card {
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  background: #f9f9f9;
  margin-bottom: 1rem;
}

.statistics-card h2 {
  margin-bottom: 0.5rem;
}

.statistics-card ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.statistics-card li {
  margin-bottom: 0.25rem;
}
</style>
