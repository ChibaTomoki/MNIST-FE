<script setup lang="ts">
type Props = {
  modelValue: string | number
  options: { value: string | number; label: string }[]
}
type Emits = {
  (e: 'update:modelValue', value: string | number): void
}

defineProps<Props>()
const emits = defineEmits<Emits>()

const emitSelected = (e: Event): void => {
  const target = e.target
  if (!target) return
  if (!(target instanceof HTMLSelectElement)) return

  emits('update:modelValue', target.value)
}
</script>

<template>
  <select :value="modelValue" @change="emitSelected">
    <option v-for="option in options" :value="option.value">
      {{ option.label }}
    </option>
  </select>
</template>

<style scoped lang="scss">
select {
  padding: 4px;
  border-radius: 4px;
}
</style>
