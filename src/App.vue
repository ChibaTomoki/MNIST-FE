<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import MyCanvas from './components/MyCanvas.vue'

const base64CanvasImg = ref<string | null>(null)
const postBase64CanvasImg = async (base64Img: string | null) => {
  if (!base64Img) return

  const res = await axios.post('http://localhost:8000/analyze-images/', {
    image_base64: base64Img,
  })
  alert(`結果: ${res.data.num}\n確率: ${res.data.prob}`)
}
</script>

<template>
  <MyCanvas
    @end-drawing="
      (base64Img) => {
        base64CanvasImg = base64Img
      }
    "
  />
  <button @click="postBase64CanvasImg(base64CanvasImg)">解析</button>
</template>

<style scoped></style>
