<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import MyWaitAnimation from './components/MyWaitAnimation.vue'
import MyCanvas from './components/MyCanvas.vue'
import MyButton from './components/MyButton.vue'
import MyDialog from './components/MyDialog.vue'

const myCanvasRef = ref<InstanceType<typeof MyCanvas> | null>(null)
const myDialogRef = ref<InstanceType<typeof MyDialog> | null>(null)
const base64CanvasImg = ref<string | null>(null)
const isLoading = ref(false)
const analyzedResult = ref<{ number: number | null; probability: number | null }>({
  number: null,
  probability: null,
})

const clearCanvas = () => {
  if (!myCanvasRef.value) return
  myCanvasRef.value.clearCanvas()
}
const postBase64CanvasImg = async (base64Img: string | null) => {
  if (!base64Img) return

  isLoading.value = true
  const res = await axios.post(`${import.meta.env.VITE_BE_BASE_URL}/analyze-images/`, {
    image_base64: base64Img,
  })
  analyzedResult.value = {
    number: res.data.num,
    probability: Math.round(res.data.prob * 100),
  }
  isLoading.value = false
  if (!myDialogRef.value) return

  myDialogRef.value.open()
}
</script>

<template>
  <MyWaitAnimation v-show="isLoading" />
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
  <MyDialog ref="myDialogRef" @on-close="clearCanvas">
    <p>結果: {{ analyzedResult.number }}</p>
    <p>確率: {{ analyzedResult.probability }}%</p>
  </MyDialog>
</template>

<style scoped lang="scss">
.button-group {
  display: flex;
  gap: 4px;
}
</style>
