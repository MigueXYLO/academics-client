<template>
  <form @submit.prevent="create">
    <div>Code:
      <input v-model="courseForm.code" type="text"></div>
    <div>Name:
      <input v-model="courseForm.name" type="text"></div>

    <button type="reset">RESET</button>
    <button type="submit">CREATE</button>
    <nuxt-link to="/">Return</nuxt-link>
  </form>
  {{ message }}
</template>
<script setup>
  const courseForm = reactive({
    name: '',
    code: ''
  })
  const message = ref('')
  const config = useRuntimeConfig()
  const api = config.public.API_URL
  const { data: courses } = await useFetch(`${api}/course`)
  async function create() {
    const requestOptions = {
      method: 'POST',
      headers: { "Content-Type": "application/json"},
      body: courseForm
    }
    const { error } = await useFetch(`${api}/course`, requestOptions)
    if (!error.value) navigateTo('/index_courses')
    message.value = error.value
  }
</script>