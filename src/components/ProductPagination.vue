<template>
  <nav class="flex justify-center mt-4" aria-label="Pagination Navigation">
    <button
      v-for="page in totalPages"
      :key="page"
      @click="changePage(page)"
      :class="[
        'mx-1 px-3 py-1 rounded',
        currentPage === page
          ? 'bg-blue-600 text-white'
          : 'bg-gray-200 text-gray-700 hover:bg-gray-300',
      ]"
      :aria-current="currentPage === page ? 'page' : false"
    >
      {{ page }}
    </button>
  </nav>
</template>

<script setup>
import { computed } from 'vue'

// Props
const props = defineProps({
  totalItems: Number,
  itemsPerPage: Number,
  currentPage: Number,
})

// Emit event
const emit = defineEmits(['update:currentPage'])

// Compute total pages
const totalPages = computed(() => {
  return Math.ceil(props.totalItems / props.itemsPerPage) || 1
})

// Change page handler
function changePage(page) {
  if (page !== props.currentPage) {
    emit('update:currentPage', page)
  }
}
</script>
