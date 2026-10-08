<script setup>
import { computed, reactive, watch } from 'vue'

const props = defineProps({
  student: {
    type: Object,
    default: null
  },
  isEditing: Boolean
})

const emit = defineEmits(['save', 'cancel'])

const form = reactive({
  studentId: '',
  name: '',
  email: '',
  course: '',
  yearLevel: '',
  contact: '',
  address: ''
})

const errors = reactive({})
const title = computed(() => (props.isEditing ? 'Edit Student' : 'Add Student'))
const buttonLabel = computed(() => (props.isEditing ? 'Update Student' : 'Save Student'))

watch(
  () => props.student,
  (student) => {
    if (student) {
      Object.assign(form, student)
    } else {
      Object.assign(form, {
        studentId: '',
        name: '',
        email: '',
        course: '',
        yearLevel: '',
        contact: '',
        address: ''
      })
    }
    errors.studentId = ''
    errors.name = ''
    errors.email = ''
    errors.course = ''
    errors.yearLevel = ''
  },
  { immediate: true }
)

function validate() {
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  errors.studentId = form.studentId.trim() ? '' : 'Student ID is required.'
  errors.name = form.name.trim() ? '' : 'Full name is required.'
  errors.email = form.email.trim() && emailPattern.test(form.email.trim())
    ? ''
    : 'Enter a valid email address.'
  errors.course = form.course.trim() ? '' : 'Course is required.'
  errors.yearLevel = form.yearLevel.trim() ? '' : 'Year level is required.'

  return !Object.values(errors).some(Boolean)
}

function submit() {
  if (!validate()) return

  emit('save', {
    studentId: form.studentId.trim(),
    name: form.name.trim(),
    email: form.email.trim(),
    course: form.course.trim(),
    yearLevel: form.yearLevel.trim(),
    contact: form.contact.trim(),
    address: form.address.trim()
  })
}
</script>

<template>
  <section class="panel form-panel" aria-labelledby="student-form-title">
    <div class="panel-heading">
      <div>
        <p class="eyebrow">Student record</p>
        <h2 id="student-form-title">{{ title }}</h2>
      </div>
      <span class="mode-badge">{{ props.isEditing ? 'Editing' : 'New record' }}</span>
    </div>

    <form class="student-form" @submit.prevent="submit" novalidate>
      <div class="form-field">
        <label for="studentId">Student ID <span>*</span></label>
        <input id="studentId" v-model="form.studentId" type="text" placeholder="e.g. 2026-003" />
        <small v-if="errors.studentId" class="field-error">{{ errors.studentId }}</small>
      </div>

      <div class="form-field">
        <label for="name">Full name <span>*</span></label>
        <input id="name" v-model="form.name" type="text" placeholder="Full name" />
        <small v-if="errors.name" class="field-error">{{ errors.name }}</small>
      </div>

      <div class="form-field">
        <label for="email">Email <span>*</span></label>
        <input id="email" v-model="form.email" type="email" placeholder="student@example.com" />
        <small v-if="errors.email" class="field-error">{{ errors.email }}</small>
      </div>

      <div class="form-field">
        <label for="course">Course <span>*</span></label>
        <input id="course" v-model="form.course" type="text" placeholder="BSIT" />
        <small v-if="errors.course" class="field-error">{{ errors.course }}</small>
      </div>

      <div class="form-field">
        <label for="yearLevel">Year level <span>*</span></label>
        <select id="yearLevel" v-model="form.yearLevel">
          <option value="">Select year</option>
          <option>1st Year</option>
          <option>2nd Year</option>
          <option>3rd Year</option>
          <option>4th Year</option>
        </select>
        <small v-if="errors.yearLevel" class="field-error">{{ errors.yearLevel }}</small>
      </div>

      <div class="form-field">
        <label for="contact">Contact number</label>
        <input id="contact" v-model="form.contact" type="tel" placeholder="Phone number" />
      </div>

      <div class="form-field form-field-wide">
        <label for="address">Address</label>
        <textarea id="address" v-model="form.address" rows="3" placeholder="Home address"></textarea>
      </div>

      <div class="form-actions form-field-wide">
        <button v-if="props.isEditing" class="button button-secondary" type="button" @click="emit('cancel')">
          Cancel
        </button>
        <button class="button button-primary" type="submit">{{ buttonLabel }}</button>
      </div>
    </form>
  </section>
</template>
