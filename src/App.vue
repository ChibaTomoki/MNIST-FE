<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import MyWaitAnimation from './components/MyWaitAnimation.vue'
import MyCanvas from './components/MyCanvas.vue'
import MyButton from './components/MyButton.vue'
import MyDialog from './components/MyDialog.vue'
import MySelect from './components/MySelect.vue'

const myCanvasRef = ref<InstanceType<typeof MyCanvas> | null>(null)
const myDialogRef = ref<InstanceType<typeof MyDialog> | null>(null)
const base64CanvasImg = ref<string | null>(null)
const lineWidth = ref<8 | 16 | 24>(16)
const isLoading = ref(false)
const analyzedResult = ref<{ number: number | null; probability: number | null }>({
  number: null,
  probability: null,
})

const clearCanvas = () => {
  if (!myCanvasRef.value) return
  myCanvasRef.value.clearCanvas()
  base64CanvasImg.value = null
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

  <div class="canvas-group">
    <MyCanvas
      ref="myCanvasRef"
      :line-width="lineWidth"
      @end-drawing="
        (base64Img) => {
          base64CanvasImg = base64Img
        }
      "
    />
    <div>
      <strong>線の太さ: </strong>
      <MySelect
        v-model.number="lineWidth"
        :options="[
          { value: 8, label: '細い' },
          { value: 16, label: '普通' },
          { value: 24, label: '太い' },
        ]"
      />
    </div>
  </div>
  <div class="button-group">
    <MyButton @on-click="postBase64CanvasImg(base64CanvasImg)" title="解析" :is-disabled="!base64CanvasImg" />
    <MyButton title="クリア" @on-click="clearCanvas" />
  </div>
  <MyDialog ref="myDialogRef" @on-close="clearCanvas">
    <p>結果: {{ analyzedResult.number }}</p>
    <p>確率: {{ analyzedResult.probability }}%</p>
  </MyDialog>
</template>

<style scoped lang="scss">
.canvas-group {
  display: flex;
  align-items: center;
  gap: 16px;
}
.button-group {
  display: flex;
  gap: 4px;
}
</style>
