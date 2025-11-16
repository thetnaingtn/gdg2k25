<template>
  <div class="charm">
    <img 
      :src="charmImage" 
      :alt="`Charm ${selectedCharm}`"
      class="charm-img"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { useSlideContext } from '@slidev/client'

const charmImages = ['bubbles', 'bubbletea', 'lipgloss']
const selectedCharm = ref<string>('bubbletea')
const { $page } = useSlideContext()

const charmImage = computed(() => `/imgs/charm/${selectedCharm.value}.png`)

const selectRandomCharm = () => {
  const pageIndex = ($page.value - 1) % charmImages.length
  selectedCharm.value = charmImages[pageIndex]
}

onMounted(() => {
  selectRandomCharm()
})
</script>

<style scoped>
.charm {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 100;
  pointer-events: none;
}

.charm-img {
  width: 80px;
  height: auto;
  opacity: 0.8;
  transition: opacity 0.3s ease;
}

.charm-img:hover {
  opacity: 1;
  pointer-events: auto;
  cursor: pointer;
}
</style>