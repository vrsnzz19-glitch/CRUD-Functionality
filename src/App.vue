<script setup>
import { computed, onMounted, ref } from 'vue'

const STORAGE_KEY = 'students'
const sampleStudents = [
  {
    id: 1,
    studentId: '2026-001',
    name: 'Juan Dela Cruz',
    course: 'BSIT',
    yearLevel: '3',
    email: 'juan@gmail.com'
  },
  {
    id: 2,
    studentId: '2026-002',
    name: 'Maria Santos',
    course: 'BSIT',
    yearLevel: '2',
    email: 'maria@gmail.com'
  }
]

const students = ref([])
const searchTerm = ref('')
const isFormOpen = ref(false)
const editingStudentId = ref(null)
const message = ref('')
const form = ref({
  studentId: '',
  name: '',
  course: '',
  yearLevel: '',
  email: ''
})
const errorMessage = ref('')

function loadStudents() {
  const savedStudents = localStorage.getItem(STORAGE_KEY)

  if (savedStudents) {
    const savedList = JSON.parse(savedStudents)
    students.value = Array.isArray(savedList) ? savedList : []
    return
  }

  students.value = sampleStudents.map((student) => ({ ...student }))
  saveStudents()
}

function saveStudents() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(students.value))
}

const filteredStudents = computed(() => {
  const search = searchTerm.value.trim().toLowerCase()

  if (!search) {
    return students.value
  }

  return students.value.filter((student) =>
    [student.studentId, student.name, student.course]
      .some((value) => value.toLowerCase().includes(search))
  )
})

function clearForm() {
  form.value = {
    studentId: '',
    name: '',
    course: '',
    yearLevel: '',
    email: ''
  }
  errorMessage.value = ''
}

function showForm() {
  isFormOpen.value = true
  editingStudentId.value = null
  clearForm()
}

function editStudent(student) {
  editingStudentId.value = student.id
  form.value = {
    studentId: student.studentId,
    name: student.name,
    course: student.course,
    yearLevel: student.yearLevel,
    email: student.email
  }
  errorMessage.value = ''
  isFormOpen.value = true
}

function validateForm() {
  const emailIsValid = form.value.email.includes('@')

  if (
    !form.value.studentId.trim() ||
    !form.value.name.trim() ||
    !form.value.course.trim() ||
    !form.value.yearLevel.trim() ||
    !form.value.email.trim() ||
    !emailIsValid
  ) {
    errorMessage.value = 'Please fill every field and enter a valid email address.'
    return false
  }

  return true
}

function addStudent(event) {
  event.preventDefault()

  if (!validateForm()) {
    return
  }

  students.value.push({
    id: Date.now(),
    studentId: form.value.studentId.trim(),
    name: form.value.name.trim(),
    course: form.value.course.trim(),
    yearLevel: form.value.yearLevel.trim(),
    email: form.value.email.trim()
  })

  saveStudents()
  clearForm()
  isFormOpen.value = false
  message.value = 'Student added successfully!'
}

function updateStudent(event) {
  event.preventDefault()

  if (!validateForm()) {
    return
  }

  students.value = students.value.map((student) => {
    if (student.id !== editingStudentId.value) {
      return student
    }

    return {
      ...student,
      studentId: form.value.studentId.trim(),
      name: form.value.name.trim(),
      course: form.value.course.trim(),
      yearLevel: form.value.yearLevel.trim(),
      email: form.value.email.trim()
    }
  })

  saveStudents()
  clearForm()
  isFormOpen.value = false
  editingStudentId.value = null
  message.value = 'Student updated successfully!'
}

function deleteStudent(student) {
  const confirmDelete = window.confirm('Are you sure you want to delete this student?')

  if (!confirmDelete) {
    return
  }

  students.value = students.value.filter((item) => item.id !== student.id)
  saveStudents()
  message.value = 'Student deleted successfully!'
}

function cancelForm() {
  clearForm()
  isFormOpen.value = false
  editingStudentId.value = null
}

onMounted(() => {
  loadStudents()
})
</script>

<template>
  <div class="app-container">
    <header class="header">
      <h1>Student Management System</h1>
      <p>Simple student record management</p>
    </header>

    <main>
      <section class="toolbar">
        <button class="button button-primary" type="button" @click="showForm">
          Add Student
        </button>

        <label class="search-box">
          <span>Search Student</span>
          <input
            v-model="searchTerm"
            type="search"
            placeholder="Search student..."
          />
        </label>
      </section>

      <section v-if="isFormOpen" class="form-card">
        <h2>{{ editingStudentId ? 'Edit Student' : 'Add Student' }}</h2>
        <form @submit.prevent="editingStudentId ? updateStudent : addStudent">
          <label>
            Student ID
            <input v-model="form.studentId" type="text" placeholder="Student ID" />
          </label>

          <label>
            Name
            <input v-model="form.name" type="text" placeholder="Name" />
          </label>

          <label>
            Course
            <input v-model="form.course" type="text" placeholder="Course" />
          </label>

          <label>
            Year Level
            <input v-model="form.yearLevel" type="text" placeholder="Year level" />
          </label>

          <label>
            Email
            <input v-model="form.email" type="email" placeholder="Email address" />
          </label>

          <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>

          <div class="form-actions">
            <button class="button button-secondary" type="button" @click="cancelForm">
              Cancel
            </button>
            <button class="button button-primary" type="submit">
              {{ editingStudentId ? 'Update Student' : 'Save Student' }}
            </button>
          </div>
        </form>
      </section>

      <section class="table-card">
        <h2>Student List</h2>

        <p v-if="message" class="success-message">{{ message }}</p>

        <div class="table-scroll">
          <table>
            <thead>
              <tr>
                <th>Student ID</th>
                <th>Name</th>
                <th>Course</th>
                <th>Year Level</th>
                <th>Email</th>
                <th>Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="student in filteredStudents" :key="student.id">
                <td>{{ student.studentId }}</td>
                <td>{{ student.name }}</td>
                <td>{{ student.course }}</td>
                <td>{{ student.yearLevel }}</td>
                <td><a :href="`mailto:${student.email}`">{{ student.email }}</a></td>
                <td>
                  <button class="button button-edit" type="button" @click="editStudent(student)">
                    Edit
                  </button>
                  <button class="button button-delete" type="button" @click="deleteStudent(student)">
                    Delete
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <p v-if="!filteredStudents.length" class="empty-message">No students found.</p>
      </section>
    </main>
  </div>
</template>
