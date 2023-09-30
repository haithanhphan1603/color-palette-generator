<template>
  <div class="bg-white rounded-md mx-2">
    <div class="flex justify-center items-center flex-col px-2 py-2">
      <div class="w-60 h-64 rounded-md" :style="{ 'background-color': color }"></div>
      <h1 class="py-2">{{ color }}</h1>
    </div>
  </div>
</template>

<script setup>
defineProps(['color', 'index'])
</script>

<style></style>
