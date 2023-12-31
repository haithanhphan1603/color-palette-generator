<script setup>
import axios from 'axios'
import { ref, onMounted, computed, watchEffect } from 'vue'
import ColorCard from '../components/ColorCard.vue'
import Spinner from '../components/Spinner.vue'

const colors = ref([])
const isLoading = ref(false)

const hexColors = computed(() => {
  return colors.value.map((color) => rgbToHex(color[0], color[1], color[2]))
})

const getColors = async () => {
  isLoading.value = true
  const response = await axios.post(
    'http://colormind.io/api/',
    {
      model: 'default'
    },
    {
      headers: {
        'Content-Type': 'text/plain'
      }
    }
  )
  colors.value = response.data.result
  isLoading.value = false
}

function componentToHex(c) {
  var hex = c.toString(16)
  return hex.length == 1 ? '0' + hex : hex
}

function rgbToHex(r, g, b) {
  return '#' + componentToHex(r) + componentToHex(g) + componentToHex(b)
}

const handleColorClick = async (index) => {
  await navigator.clipboard.writeText(hexColors.value[index])

  alert(`Copie ${hexColors.value[index]} to clipboard`)
}

watchEffect(() => {
  document.addEventListener('keydown', (e) => {
    if (e.code === 'Space') {
      getColors()
    }
  })
  document.addEventListener('keydown', (e) => {
    if (e.code === 'KeyC') {
      navigator.clipboard.writeText(hexColors.value.join(','))
      alert(`Copied ${hexColors.value.join(',')} to clipboard`)
    }
  })
})

onMounted(() => {
  getColors()
})
</script>

<template>
  <div class="bg-slate-100 h-screen">
    <header>
      <h1 class="text-3xl font-medium pt-16 text-center">Color pallette generator</h1>
    </header>

    <div v-if="isLoading" class="flex absolute inset-0 justify-center items-center">
      <Spinner />
    </div>

    <main v-else class="pt-16">
      <div class="flex fitems-center justify-center">
        <ColorCard
          class="cursor-pointer"
          @click="handleColorClick(index)"
          v-for="(color, index) in hexColors"
          :index="index"
          :color="color"
          :key="color"
        >
        </ColorCard>
      </div>
      <div class="pt-10 flex flex-col items-center justify-center">
        <button
          class="py-4 px-6 font-semibold rounded-lg shadow-md text-white bg-indigo-500 hover:bg-indigo-700"
          @click="getColors"
        >
          Generate new pallette
        </button>
        <div
          class="cursor-default border m-6 p-3 rounded-3xl bg-white text-gray-700 hidden md:block"
        >
          <p class="text-sm text-center flex items-center">
            Click to copy individual color. Press "C" to copy pallete
          </p>
        </div>
      </div>
    </main>
  </div>
</template>

<style lang="scss"></style>
