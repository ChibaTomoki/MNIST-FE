<script setup lang="ts">
import { ref, watchEffect } from 'vue'

type Point = {
  x: number
  y: number
}

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
}

watchEffect(() => {
  if (!canvasRef.value) return

  canvasCtx.value = canvasRef.value.getContext('2d')
  canvasCtx.value!.lineWidth = 5
  canvasCtx.value!.fillStyle = '#fff'
  canvasCtx.value!.fillRect(0, 0, 560, 560)
})
</script>

<template>
  <canvas
    ref="canvasRef"
    height="560"
    width="560"
    class="canvas"
    @mousedown="startDrawing"
    @mousemove="draw"
    @mouseup="endDrawing"
  />
</template>

<style scoped>
.canvas {
  border: solid 1px;
  height: 560px;
  width: 560px;
}
</style>
