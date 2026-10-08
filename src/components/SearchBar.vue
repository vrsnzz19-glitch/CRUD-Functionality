<script setup>
const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <label class="search-field" for="student-search">
    <span aria-hidden="true">⌕</span>
    <input
      id="student-search"
      :value="props.modelValue"
      type="search"
      placeholder="Search students..."
      @input="emit('update:modelValue', $event.target.value)"
    />
  </label>
</template>
