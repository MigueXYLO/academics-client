<template>
  <div v-if="error">
      Error: {{ error.message }}
  </div>
  <div v-else>
    <nuxt-link to="/">Home</nuxt-link>
    <br>
    <nuxt-link to="/subjects/create">Create a New Subject</nuxt-link>
    <h2>Subjects</h2>
    <table>
      <thead>
        <tr>
          <th>Code</th>
          <th>Name</th>
          <th>Course</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="subject in subjects">
          <td>{{ subject.code }}</td>
          <td>{{ subject.name }}</td>
          <td>{{ subject.courseName }}</td>
        </tr>
      </tbody>
    </table>
    <button @click.prevent="refresh">Refresh Data</button>
  </div>
</template>
<script setup>
const config = useRuntimeConfig()
const api = config.public.API_URL
const { data: subjects, error, refresh } = await useFetch(`${api}/subject`)
</script>