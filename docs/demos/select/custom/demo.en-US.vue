<template>
  <Select v-model:value="value" :options="options">
    <template #default="{ option, index }">
      {{ `${index + 1}、${option.label}` }}
    </template>
  </Select>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const value = ref('')

const options = [
  {
    label: 'Option 1',
    value: 1
  },
  {
    label: 'Option 2',
    value: 2
  },
  {
    label: 'Option 3',
    value: 3
  }
]
</script>

<style scoped>
.vxp-select {
  max-width: 400px;
}
</style>
