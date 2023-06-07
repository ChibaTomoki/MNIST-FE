<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import MyCanvas from './components/MyCanvas.vue'
import MyButton from './components/MyButton.vue'

const myCanvasRef = ref<InstanceType<typeof MyCanvas> | null>(null)
const base64CanvasImg = ref<string | null>(null)

const clearCanvas = () => {
  if (!myCanvasRef.value) return
  myCanvasRef.value.clearCanvas()
}
const postBase64CanvasImg = async (base64Img: string | null) => {
  if (!base64Img) return

  const res = await axios.post(`${import.meta.env.VITE_BE_BASE_URL}/analyze-images/`, {
    image_base64: base64Img,
  })
  alert(`結果: ${res.data.num}\n確率: ${Math.round(res.data.prob * 100)}%`)
  clearCanvas()
  // TODO: wait animationを出したい
}
</script>

<template>
  <div>
    <MyCanvas
      ref="myCanvasRef"
      @end-drawing="
        (base64Img) => {
          base64CanvasImg = base64Img
        }
      "
    />
  </div>
  <div class="button-group">
    <MyButton title="解析" @on-click="postBase64CanvasImg(base64CanvasImg)" />
    <MyButton title="クリア" @on-click="clearCanvas" />
  </div>
</template>

<style scoped lang="scss">
.button-group {
  display: flex;
  gap: 4px;
}
</style>
