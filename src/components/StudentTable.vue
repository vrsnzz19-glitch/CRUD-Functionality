<script setup>
const props = defineProps({
  students: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['view', 'edit', 'delete'])
</script>

<template>
  <section class="panel table-panel" aria-labelledby="student-list-title">
    <div class="panel-heading table-heading">
      <div>
        <p class="eyebrow">Student records</p>
        <h2 id="student-list-title">Student List</h2>
      </div>
      <span class="count-pill">{{ props.students.length }} student{{ props.students.length === 1 ? '' : 's' }}</span>
    </div>

    <div v-if="props.students.length" class="table-scroll">
      <table>
        <thead>
          <tr>
            <th>Student ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Course</th>
            <th>Year Level</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="student in props.students" :key="student.id">
            <td><strong>{{ student.studentId }}</strong></td>
            <td>{{ student.name }}</td>
            <td><a :href="`mailto:${student.email}`">{{ student.email }}</a></td>
            <td><span class="course-tag">{{ student.course }}</span></td>
            <td>{{ student.yearLevel }}</td>
            <td>
              <div class="action-group">
                <button class="button button-info button-small" type="button" @click="emit('view', student)">View</button>
                <button class="button button-warning button-small" type="button" @click="emit('edit', student)">Edit</button>
                <button class="button button-danger button-small" type="button" @click="emit('delete', student)">Delete</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-else class="empty-state">
      <span class="empty-icon">⌕</span>
      <h3>No students found.</h3>
      <p>Try another search or add a new student record.</p>
    </div>
  </section>
</template>
