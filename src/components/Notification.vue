<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue'

const props = defineProps({
  message: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['close'])
const visible = ref(true)
let timeoutId

onMounted(() => {
  timeoutId = window.setTimeout(() => {
    visible.value = false
    emit('close')
  }, 4000)
})

onBeforeUnmount(() => window.clearTimeout(timeoutId))
</script>

<template>
  <div v-if="visible && props.message" class="notification" role="status">
    <span class="notification-icon">✓</span>
    <p>{{ props.message }}</p>
    <button type="button" aria-label="Dismiss notification" @click="visible = false; emit('close')">×</button>
  </div>
</template>
