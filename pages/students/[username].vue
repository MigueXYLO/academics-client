<template>

  <div>
    <!-- Button to go back -->
    <nuxt-link to="/students">Back to Students</nuxt-link>
  </div>

  <div v-if="student">
    <h2>Details of {{ student.username }}</h2>
    <p>Name: {{ student.name }}</p>
    <p>Email: {{ student.email }}</p>
    <p>Course: {{ student.courseName }}</p>
  </div>

  <div v-if="subjects.length">
    <h2>Enrolled in:</h2>
    <ul>
      <li v-for="subject in subjects" :key="subject.code">
        {{ subject.name }} ({{ subject.schoolYear }}) - {{ subject.courseName }} ({{ subject.courseCode }})
        <br>
        <button @click="unenroll(subject.code)">Unenroll</button>
      </li>
    </ul>

    <div v-if="filteredSubjects.length">
      <h2>Enroll in:</h2>
      <select v-model="selectedSubject">
        <option v-for="subject in filteredSubjects" :key="subject.code" :value="subject.code">
          {{ subject.name }} ({{ subject.schoolYear }}) - {{ subject.courseName }}
        </option>
      </select>
      <button @click="enroll">Enroll</button>
    </div>
  </div>

  <div v-if="messages.length">
    <h2>Error messages:</h2>
    <ul>
      <li v-for="message in messages" :key="message">{{ message }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router' // replace with your actual fetch library
const route = useRoute()
const username = route.params.username
const config = useRuntimeConfig()
const api = config.public.API_URL

const messages = ref([])

// Fetch data
const { data: student, error: studentErr } = await useFetch(`${api}/student/${username}`)
const { data: subjects, error: subjectsErr } = await useFetch(`${api}/student/${username}/subjects`)
const { data: allSubjects, error: allSubjectsErr } = await useFetch(`${api}/subject/`)

if (studentErr.value) messages.value.push(studentErr.value)
if (subjectsErr.value) messages.value.push(subjectsErr.value)
if (allSubjectsErr.value) messages.value.push(allSubjectsErr.value)

// Filter subjects based on enrollment and course
const filteredSubjects = computed(() =>
    allSubjects.value.filter(subject =>
        !subjects.value.find(enrolled => enrolled.code === subject.code) &&
        subject.courseCode === student.value.courseCode
    )
)

const selectedSubject = ref(null)

const unenroll = async (subjectCode) => {
  const response = await fetch(`${api}/student/${username}/unenroll/${subjectCode}`, { method: 'POST' })
  if (!response.ok) {
    messages.value.push(`Failed to unenroll from ${subjectCode}`)
  } else {
    // Update subjects list and dropdown
    subjects.value = subjects.value.filter(subject => subject.code !== subjectCode)
  }
}

const enroll = async () => {
  if (!selectedSubject.value) return // Ensure a subject is selected

  const response = await fetch(`${api}/student/${username}/enroll/${selectedSubject.value}`, { method: 'POST' })
  if (!response.ok) {
    messages.value.push(`Failed to enroll in ${selectedSubject.value}`)
  } else {
    // Update subjects and reset the selected subject
    const newSubject = allSubjects.value.find(subject => subject.code === selectedSubject.value)
    subjects.value.push(newSubject)
    selectedSubject.value = null
  }
}
</script>
