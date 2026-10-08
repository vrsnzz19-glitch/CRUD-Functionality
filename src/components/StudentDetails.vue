<script setup>
const props = defineProps({
  student: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['close', 'edit'])
</script>

<template>
  <div v-if="props.student" class="modal-backdrop" role="presentation" @click="emit('close')">
    <section class="modal-card" role="dialog" aria-modal="true" aria-labelledby="details-title" @click.stop>
      <div class="modal-header">
        <div>
          <p class="eyebrow">Student details</p>
          <h2 id="details-title">{{ props.student.name }}</h2>
        </div>
        <button class="icon-button" type="button" aria-label="Close details" @click="emit('close')">×</button>
      </div>

      <div class="details-grid">
        <div><span>Student ID</span><strong>{{ props.student.studentId }}</strong></div>
        <div><span>Full name</span><strong>{{ props.student.name }}</strong></div>
        <div><span>Email</span><a :href="`mailto:${props.student.email}`">{{ props.student.email }}</a></div>
        <div><span>Course</span><strong>{{ props.student.course }}</strong></div>
        <div><span>Year level</span><strong>{{ props.student.yearLevel }}</strong></div>
        <div><span>Contact number</span><strong>{{ props.student.contact || 'Not provided' }}</strong></div>
        <div class="details-wide"><span>Address</span><strong>{{ props.student.address || 'Not provided' }}</strong></div>
      </div>

      <div class="modal-actions">
        <button class="button button-secondary" type="button" @click="emit('close')">Close</button>
        <button class="button button-warning" type="button" @click="emit('edit', props.student)">Edit Student</button>
      </div>
    </section>
  </div>
</template>
