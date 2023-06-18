<script setup lang="ts">
import { ref } from 'vue'
import MyButton from './MyButton.vue'

type Emits = {
  (e: 'onClose'): void
}

const emits = defineEmits<Emits>()

const dialogRef = ref<HTMLDialogElement>()

const open = () => {
  if (!dialogRef.value) return
  dialogRef.value?.showModal()
}
const close = () => {
  if (!dialogRef.value) return
  dialogRef.value?.close()
  emits('onClose')
}

defineExpose({ open })
</script>

<template>
  <dialog ref="dialogRef">
    <div class="main">
      <slot></slot>
    </div>
    <div class="footer">
      <MyButton title="閉じる" @on-click="close" />
    </div>
  </dialog>
</template>

<style scoped lang="scss">
dialog {
  margin-top: 40px;
  padding: 0;
  border: 1px solid #aaa;
  border-radius: 4px;

  &::backdrop {
    background: rgba(0, 0, 0, 0.3);
  }
}
.main {
  padding: 8px;
}
.footer {
  padding: 8px;
  border-top: 1px solid #000;
}
</style>
