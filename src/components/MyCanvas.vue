<script setup lang="ts">
import { ref, watchEffect } from 'vue'

type Emits = {
  (e: 'endDrawing', base64Img: string): void
}
type Point = {
  x: number
  y: number
}

const emits = defineEmits<Emits>()
const canvasRef = ref<HTMLCanvasElement>()
const canvasCtx = ref<CanvasRenderingContext2D | null>(null)
const startPoint = ref<Point | null>(null)

const startDrawing = (e: MouseEvent) => {
  startPoint.value = {
    x: e.offsetX,
    y: e.offsetY,
  }
}
const draw = (e: MouseEvent) => {
  if (!canvasCtx.value) return
  if (!startPoint.value) return

  canvasCtx.value.beginPath()
  canvasCtx.value.moveTo(startPoint.value.x, startPoint.value.y)
  canvasCtx.value.lineTo(e.offsetX, e.offsetY)
  canvasCtx.value.stroke()
  startPoint.value.x = e.offsetX
  startPoint.value.y = e.offsetY
}
const endDrawing = () => {
  startPoint.value = null
  emits('endDrawing', canvasRef.value!.toDataURL('image/png'))
}

watchEffect(() => {
  if (!canvasRef.value) return

  canvasCtx.value = canvasRef.value.getContext('2d')
  canvasCtx.value!.lineWidth = 10
  canvasCtx.value!.fillStyle = '#fff'
  canvasCtx.value!.fillRect(0, 0, 140, 140)
})
</script>

<template>
  <canvas
    ref="canvasRef"
    height="140"
    width="140"
    class="canvas"
    @mousedown="startDrawing"
    @mousemove="draw"
    @mouseup="endDrawing"
  />
</template>

<style scoped>
.canvas {
  border: solid 1px;
  height: 140px;
  width: 140px;
}
</style>
