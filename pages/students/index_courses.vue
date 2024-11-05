<template>
  <div v-if="error">Error: {{ error.message }}</div>
  <div v-else>
    <nuxt-link to="/create_course">Create a New Course</nuxt-link>
    <h2>Students</h2>
    <table>
      <thead>
      <tr><th>Code</th><th>Name</th></tr>
      </thead>
      <tbody>
      <tr v-for="course in courses">
        <td>{{ course.code }}</td>
        <td>{{ course.name }}</td>
      </tr>
      </tbody>
    </table>
  </div>
  <button @click.prevent="refresh">Refresh Data</button>
</template>
<script setup>
const config = useRuntimeConfig()
const api = config.public.API_URL
const { data: courses, error, refresh } = await useFetch(`${api}/course`)
</script>