<template>
  <div v-if="error">Error: {{ error.message }}</div>
  <div v-else>
    <nuxt-link to="/students">View Students</nuxt-link>
    <nuxt-link to="/courses/create">Create a New Course</nuxt-link>
    <h2>Students</h2>
    <table>
      <thead>
      <tr><th>Code</th><th>Name</th><th>Actions</th></tr>
      </thead>
      <tbody>
      <tr v-for="course in courses">
        <td>{{ course.code }}</td>
        <td>{{ course.name }}</td>
        <td><button @onclick="delete_c(course.code)">Delete</button></td>
      </tr>
      </tbody>
    </table>
  </div>
  <button @click.prevent="refresh">Refresh Data</button>

  <div v-if="messages.length">
    <h2>Error messages:</h2>
    <ul>
      <li v-for="message in messages" :key="message">{{ message }}</li>
    </ul>
  </div>
</template>
<script setup>
const config = useRuntimeConfig()
const api = config.public.API_URL
const { data: courses, error, refresh } = await useFetch(`${api}/course`)
const delete_c = async (code) => {
  const response = await fetch(`${api}/course/${code}`, { method: 'DELETE' })
}
</script>