<script setup>
import { computed, onMounted, ref } from 'vue'
import Notification from './components/Notification.vue'
import SearchBar from './components/SearchBar.vue'
import StudentDetails from './components/StudentDetails.vue'
import StudentForm from './components/StudentForm.vue'
import StudentTable from './components/StudentTable.vue'

const STORAGE_KEY = 'students'
const SAMPLE_STUDENTS = [
  {
    id: 1,
    studentId: '2026-001',
    name: 'Juan Dela Cruz',
    email: 'juan@gmail.com',
    course: 'BSIT',
    yearLevel: '3rd Year',
    contact: '09123456789',
    address: 'Tubigon, Bohol'
  },
  {
    id: 2,
    studentId: '2026-002',
    name: 'Maria Santos',
    email: 'maria@gmail.com',
    course: 'BSIT',
    yearLevel: '2nd Year',
    contact: '09987654321',
    address: 'Tagbilaran City, Bohol'
  }
]

const students = ref([])
const searchTerm = ref('')
const selectedStudent = ref(null)
const editingStudent = ref(null)
const isFormOpen = ref(false)
const notification = ref('')

function loadStudents() {
  try {
    const savedStudents = localStorage.getItem(STORAGE_KEY)

    if (savedStudents) {
      const parsed = JSON.parse(savedStudents)
      if (Array.isArray(parsed) && parsed.length > 0) {
        students.value = parsed
        return
      }
    }

    students.value = SAMPLE_STUDENTS.map((student) => ({ ...student }))
    saveStudents()
  } catch (error) {
    console.error('Unable to load students.', error)
    students.value = []
  }
}

function saveStudents() {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(students.value))
  } catch (error) {
    console.error('Unable to save students.', error)
  }
}

const filteredStudents = computed(() => {
  const query = searchTerm.value.trim().toLowerCase()
  if (!query) return students.value

  return students.value.filter((student) =>
    [student.studentId, student.name, student.email, student.course]
      .some((value) => value.toLowerCase().includes(query))
  )
})

function showNotification(message) {
  notification.value = message
}

function openAddForm() {
  editingStudent.value = null
  selectedStudent.value = null
  isFormOpen.value = true
}

function openEditForm(student) {
  selectedStudent.value = null
  editingStudent.value = student
  isFormOpen.value = true
}

function cancelForm() {
  editingStudent.value = null
  isFormOpen.value = false
}

function saveStudent(studentData) {
  const normalizedStudentId = studentData.studentId.trim().toUpperCase()
  const existingStudent = students.value.find(
    (student) => student.studentId.toUpperCase() === normalizedStudentId
  )

  if (editingStudent.value) {
    if (existingStudent && existingStudent.id !== editingStudent.value.id) {
      showNotification('Student ID already exists.')
      return
    }

    students.value = students.value.map((student) =>
      student.id === editingStudent.value.id
        ? { ...student, ...studentData, studentId: normalizedStudentId, id: student.id }
        : student
    )
    showNotification('Student updated successfully!')
  } else {
    if (existingStudent) {
      showNotification('Student ID already exists.')
      return
    }

    students.value.push({ id: Date.now(), ...studentData, studentId: normalizedStudentId })
    showNotification('Student added successfully!')
  }

  saveStudents()
  cancelForm()
}

function viewStudent(student) {
  selectedStudent.value = student
}

function closeDetails() {
  selectedStudent.value = null
}

function editFromDetails(student) {
  closeDetails()
  openEditForm(student)
}

function deleteStudent(student) {
  const confirmed = window.confirm('Are you sure you want to delete this student?')
  if (!confirmed) return

  students.value = students.value.filter((item) => item.id !== student.id)
  saveStudents()
  showNotification('Student deleted successfully!')
}

onMounted(() => {
  loadStudents()
})
</script>

<template>
  <div class="app-shell">
    <header class="app-header">
      <div class="brand-mark" aria-hidden="true">SM</div>
      <div>
        <p class="eyebrow">Academic records</p>
        <h1>Student Management System</h1>
        <p>Manage student records easily</p>
      </div>
      <div class="header-stat">
        <span>Total Students</span>
        <strong>{{ students.length }}</strong>
      </div>
    </header>

    <main>
      <section class="dashboard-summary" aria-label="Dashboard summary">
        <div class="summary-card">
          <span class="summary-icon blue">◎</span>
          <div><span>Total Students</span><strong>{{ students.length }}</strong></div>
        </div>
        <div class="summary-card">
          <span class="summary-icon green">✓</span>
          <div><span>Records Saved</span><strong>Local</strong></div>
        </div>
        <div class="summary-card">
          <span class="summary-icon purple">↗</span>
          <div><span>Search Results</span><strong>{{ filteredStudents.length }}</strong></div>
        </div>
      </section>

      <div class="content-grid">
        <StudentForm
          v-if="isFormOpen"
          :student="editingStudent"
          :is-editing="Boolean(editingStudent)"
          @save="saveStudent"
          @cancel="cancelForm"
        />
        <section v-else class="panel add-panel">
          <div class="panel-heading">
            <div>
              <p class="eyebrow">New record</p>
              <h2>Add Student</h2>
            </div>
          </div>
          <p class="add-description">Enter the required student details to create a new record.</p>
          <button class="button button-primary add-button" type="button" @click="openAddForm">
            <span>＋</span> Add Student
          </button>
        </section>
      </div>

      <section class="toolbar" aria-label="Student list controls">
        <div>
          <p class="eyebrow">Student directory</p>
          <h2>Student List</h2>
        </div>
        <SearchBar v-model="searchTerm" />
      </section>

      <StudentTable
        :students="filteredStudents"
        @view="viewStudent"
        @edit="openEditForm"
        @delete="deleteStudent"
      />
    </main>

    <StudentDetails
      :student="selectedStudent"
      @close="closeDetails"
      @edit="editFromDetails"
    />

    <Notification v-if="notification" :message="notification" @close="notification = ''" />
  </div>
</template>
