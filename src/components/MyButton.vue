<script setup lang="ts">
type Props = {
  title: string
  isDisabled: boolean
}
type Emits = {
  (e: 'onClick'): void
}

withDefaults(defineProps<Props>(), {
  isDisabled: false,
})
const emits = defineEmits<Emits>()
</script>

<template>
  <button @click="emits('onClick')" :disabled="isDisabled">{{ title }}</button>
</template>

<style scoped lang="scss">
button {
  color: inherit;
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #f9f9f9;
  cursor: pointer;
  transition: border-color background-color 0.25s;
}
button:hover {
  background-color: #e9e9e9;
}
button:focus,
button:focus-visible {
  outline: 4px auto -webkit-focus-ring-color;
}
</style>
