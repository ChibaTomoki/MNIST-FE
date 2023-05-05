<script setup lang="ts">
import { ref } from 'vue'
import MyCanvas from './components/MyCanvas.vue'

const base64CanvasImg = ref<string | null>(null)
</script>

<template>
  <MyCanvas
    @end-drawing="
      (base64Img) => {
        base64CanvasImg = base64Img
      }
    "
  />
</template>

<style scoped></style>
