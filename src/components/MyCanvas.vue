<script setup lang="ts">
import { ref, watchEffect } from 'vue'

type Props = {
  lineWidth: 8 | 16 | 24
}
type Emits = {
  (e: 'endDrawing', base64Img: string): void
}
type Point = {
  x: number
  y: number
}

const props = defineProps<Props>()
const emits = defineEmits<Emits>()
const canvasRef = ref<HTMLCanvasElement>()
const canvasCtx = ref<CanvasRenderingContext2D | null>(null)
const startPoint = ref<Point | null>(null)
const canvasOffset = ref<DOMRect | null>(null)

const startDrawing = (e: MouseEvent) => {
  startPoint.value = {
    x: e.offsetX,
    y: e.offsetY,
  }
}
const startDrawingByTouch = (e: TouchEvent) => {
  if (!canvasOffset.value) return

  startPoint.value = {
    x: e.changedTouches[0].pageX - canvasOffset.value.left,
    y: e.changedTouches[0].pageY - canvasOffset.value.top,
  }
}
const draw = (e: MouseEvent) => {
  if (!canvasCtx.value) return
  if (!startPoint.value) return

  canvasCtx.value.beginPath()
  canvasCtx.value.arc(startPoint.value.x, startPoint.value.y, props.lineWidth / 2, 0, 2 * Math.PI)
  canvasCtx.value.fill()
  canvasCtx.value.beginPath()
  canvasCtx.value.moveTo(startPoint.value.x, startPoint.value.y)
  canvasCtx.value.lineTo(e.offsetX, e.offsetY)
  canvasCtx.value.stroke()

  startPoint.value.x = e.offsetX
  startPoint.value.y = e.offsetY
}
const drawByTouch = (e: TouchEvent) => {
  if (!canvasCtx.value) return
  if (!startPoint.value) return
  if (!canvasOffset.value) return

  e.preventDefault()

  canvasCtx.value.beginPath()
  canvasCtx.value.arc(startPoint.value.x, startPoint.value.y, props.lineWidth / 2, 0, 2 * Math.PI)
  canvasCtx.value.fill()
  canvasCtx.value.beginPath()
  canvasCtx.value.moveTo(startPoint.value.x, startPoint.value.y)
  canvasCtx.value.lineTo(
    e.changedTouches[0].pageX - canvasOffset.value.left,
    e.changedTouches[0].pageY - canvasOffset.value.top
  )
  canvasCtx.value.stroke()

  startPoint.value.x = e.changedTouches[0].pageX - canvasOffset.value.left
  startPoint.value.y = e.changedTouches[0].pageY - canvasOffset.value.top
}
const endDrawing = () => {
  if (!canvasCtx.value) return
  if (!startPoint.value) return
  if (!canvasRef.value) return

  canvasCtx.value.beginPath()
  canvasCtx.value.arc(startPoint.value.x, startPoint.value.y, props.lineWidth / 2, 0, 2 * Math.PI)
  canvasCtx.value.fill()

  startPoint.value = null
  emits('endDrawing', canvasRef.value.toDataURL('image/png'))
}
const clearCanvas = () => {
  if (!canvasCtx.value) return

  canvasCtx.value.fillStyle = '#fff'
  canvasCtx.value.fillRect(0, 0, 160, 160)
  canvasCtx.value.fillStyle = '#000'
}

defineExpose({ clearCanvas })

watchEffect(() => {
  if (!canvasRef.value) return

  canvasCtx.value = canvasRef.value.getContext('2d')
  if (!canvasCtx.value) return

  canvasCtx.value.fillStyle = '#fff'
  canvasCtx.value.fillRect(0, 0, 160, 160)
  canvasCtx.value.fillStyle = '#000'
  canvasOffset.value = canvasRef.value.getBoundingClientRect()
})
watchEffect(() => {
  if (!canvasCtx.value) return
  canvasCtx.value.lineWidth = props.lineWidth
})
</script>

<template>
  <canvas
    ref="canvasRef"
    height="160"
    width="160"
    class="canvas"
    @mousedown="startDrawing"
    @mousemove="draw"
    @mouseup="endDrawing"
    @mouseleave="endDrawing"
    @touchstart="startDrawingByTouch"
    @touchmove="drawByTouch"
    @touchend="endDrawing"
  />
</template>

<style scoped>
.canvas {
  border: solid 1px;
  height: 160px;
  width: 160px;
}
</style>
