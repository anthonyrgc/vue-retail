<template>
  <div class="container mx-auto p-4 space-y-6">
    <!-- Componente de estadisticas -->
    <ProductStatistics :products="filteredProducts" />

    <!-- Vista principal en donde muestro los filtros de busqueda a la izquierda y productos a la derecha -->
    <div class="flex flex-col lg:flex-row gap-6">
      <!-- Filtros de búsqueda -->
      <aside class="w-full lg:w-1/4 space-y-4 border rounded-lg p-4 bg-white shadow-md">
        <h2 class="text-lg font-bold text-center border-b pb-2 mb-4">Filtros de búsqueda</h2>
        <!-- Filtro de categoría -->
        <div>
          <h3 class="font-semibold mb-1">Categoría</h3>
          <select v-model="categoryFilter" class="border p-2 rounded w-full">
            <option value="">Todas las categorías</option>
            <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
          </select>
        </div>
        <!-- Fitro de rango de precio -->
        <div>
          <h3 class="font-semibold mb-2 mt-4">Rango de precios (USD)</h3>
          <div class="flex gap-2">
            <input
              type="number"
              v-model.number="minPriceFilter"
              placeholder="Min"
              class="border p-2 rounded w-1/2"
            />
            <input
              type="number"
              v-model.number="maxPriceFilter"
              placeholder="Max"
              class="border p-2 rounded w-1/2"
            />
          </div>
        </div>
        <!-- Botón para guardar preferencias -->
        <button
          @click="guardarPreferencias"
          class="mt-6 w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700 transition"
        >
          Guardar preferencias
        </button>
      </aside>
      <!-- Sección de productos -->
      <section class="w-full lg:w-3/4 space-y-6">
        <!-- Grid de productos con animación -->
        <TransitionGroup
          name="fade-slide"
          tag="div"
          class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4"
        >
          <ProductCard
            v-for="product in paginatedProducts"
            :key="product.id"
            :product="product"
            :exchangeRate="exchangeRate"
          />
        </TransitionGroup>

        <!-- Mensaje animado para cuando no hay productos encontrados con esos filtros -->
        <Transition name="fade-slide">
          <div
            v-if="!loading && filteredProducts.length === 0"
            class="mt-6 bg-gray-100 text-gray-700 p-6 rounded-xl shadow-md flex items-center justify-center flex-col"
          >
            <div class="text-5xl mb-3">😔</div>
            <div class="text-lg font-semibold">No hay productos encontrados</div>
            <div class="text-sm mt-2 text-center max-w-sm">
              <span v-if="categoryFilter">
                en la categoría "<strong>{{ categoryFilter }}</strong
                >"
              </span>
              <span v-if="minPriceFilter || maxPriceFilter">
                <template v-if="categoryFilter"> y </template>
                dentro del rango de precio entre <strong>\${{ minPriceFilter }}</strong> y
                <strong>\${{ maxPriceFilter }}</strong> </span
              >.
            </div>
          </div>
        </Transition>

        <!-- Paginación -->
        <ProductPagination
          :totalItems="filteredProducts.length"
          :itemsPerPage="itemsPerPage"
          :currentPage="currentPage"
          @update:currentPage="(page) => (currentPage = page)"
        />

        <!-- Loading para carga de productos y mensaje de errores -->
        <div v-if="loading" class="text-center">Cargando productos...</div>
        <div v-if="error" class="text-center text-red-600">{{ error }}</div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'
import ProductCard from './components/ProductCard.vue'
import ProductPagination from './components/ProductPagination.vue'
import ProductStatistics from './components/ProductStatistics.vue'

// Variables
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

// Función para guardar filtros en localStorage
const guardarPreferencias = () => {
  const preferencias = {
    category: categoryFilter.value,
    minPrice: minPriceFilter.value,
    maxPrice: maxPriceFilter.value,
  }
  localStorage.setItem('filtrosBusqueda', JSON.stringify(preferencias))
  alert('Preferencias guardadas.')
}

// Función para cargar filtros desde localStorage (si existen)
const cargarPreferencias = () => {
  const saved = localStorage.getItem('filtrosBusqueda')
  if (saved) {
    try {
      const prefs = JSON.parse(saved)
      categoryFilter.value = prefs.category ?? ''
      minPriceFilter.value = prefs.minPrice ?? 0
      maxPriceFilter.value = prefs.maxPrice ?? 1000
    } catch (e) {
      console.warn('Error al cargar filtros guardados', e)
    }
  }
}

// Carga inicial de datos
onMounted(async () => {
  loading.value = true
  cargarPreferencias()

  try {
    const [productsRes, categoriesRes, exchangeRes] = await Promise.all([
      axios.get('https://fakestoreapi.com/products'),
      axios.get('https://fakestoreapi.com/products/categories'),
      axios.get('https://pydolarve.org/api/v1/dollar?page=bcv'),
    ])

    products.value = productsRes.data
    categories.value = categoriesRes.data
    // Aquí accedemos al precio del dolar bcv de banesco
    exchangeRate.value = exchangeRes.data.monitors.banesco.price
  } catch (e) {
    console.error(e)
    error.value = 'Failed to load data. Please try again later.'
  } finally {
    loading.value = false
  }
})

// filtro de productos
const filteredProducts = computed(() => {
  return products.value.filter((p) => {
    const matchCategory = categoryFilter.value ? p.category === categoryFilter.value : true
    const matchPrice = p.price >= minPriceFilter.value && p.price <= maxPriceFilter.value
    return matchCategory && matchPrice
  })
})

// Paginación de productos
const paginatedProducts = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage
  return filteredProducts.value.slice(start, start + itemsPerPage)
})
</script>

<style>
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.8s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(10px);
}

.fade-slide-enter-to,
.fade-slide-leave-from {
  opacity: 1;
  transform: translateY(0);
}
</style>
