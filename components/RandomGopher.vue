<template>
  <div class="random-gopher">
    <img 
      :src="gopherImage" 
      :alt="`Gopher ${selectedGopher}`"
      class="gopher-img"
    />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useSlideContext } from '@slidev/client'

const gopherNumbers = [1, 2, 3, 20, 21, 33, 41, 50]

// Use current page number to select gopher (ensures unique gopher per slide)
const { $page } = useSlideContext()
const selectedGopher = computed(() => {
  const pageIndex = ($page.value - 1) % gopherNumbers.length
  return gopherNumbers[pageIndex]
})

const gopherImage = computed(() => `/imgs/gophers/${selectedGopher.value}.png`)
</script>

<style scoped>
.random-gopher {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 100;
  pointer-events: none;
}

.gopher-img {
  width: 120px;
  height: auto;
  opacity: 0.8;
  transition: opacity 0.3s ease;
}

.gopher-img:hover {
  opacity: 1;
  pointer-events: auto;
  cursor: pointer;
}
</style>