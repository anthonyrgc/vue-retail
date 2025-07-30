<template>
  <div class="border rounded p-4 shadow hover:shadow-lg transition">
    <img
      :src="props.product.image"
      :alt="props.product.title"
      loading="lazy"
      class="w-full h-48 object-contain mb-2"
    />
    <h3 class="font-semibold text-lg mb-1">{{ props.product.title }}</h3>
    <p class="text-sm text-gray-600 capitalize mb-1">Categoría: {{ props.product.category }}</p>
    <p class="text-base font-bold mb-1">Precio USD: ${{ props.product.price.toFixed(2) }}</p>
    <p class="text-base font-bold text-yellow-700">
      Precio (VES): {{ priceInVES.toLocaleString('es-VE', { style: 'currency', currency: 'VES' }) }}
    </p>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  product: Object,
  exchangeRate: Number,
})

const priceInVES = computed(() => {
  return Number((props.product.price * props.exchangeRate).toFixed(2))
})
</script>
