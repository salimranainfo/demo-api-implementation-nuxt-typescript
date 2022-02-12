<template>
  <div class="h-full flex justify-center items-center">
    <main class="max-w-[1100px] mx-auto bg-white p-6 rounded-md shadow-md">
      <table v-if="users.length && !loading" class="table text-lg">
        <thead>
          <tr>
            <th>ID</th>
            <th>Username</th>
            <th>Email</th>
            <th>Company Name</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="user in users" :key="user.id">
            <td>{{ user.id }}</td>
            <td>
              <nuxt-link
                :to="`/${user.id}`"
                class="font-bold underline text-indigo-600"
                >{{ user.username }}</nuxt-link
              >
            </td>
            <td>{{ user.email }}</td>
            <td>{{ (user.company && user.company.name) || '' }}</td>
          </tr>
        </tbody>
      </table>

      <div v-if="!users.length && !loading" class="font-bold">
        Could not fetch Users from database
      </div>

      <div v-if="loading" class="font-bold">Loading ....</div>
    </main>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

import User from '@/types/User'

export default Vue.extend({
  name: 'IndexPage',

  data() {
    return {
      users: [] as User[],
      loading: true,
    }
  },

  async fetch() {
    try {
      const users = await this.$axios.$get('/users')

      if (users && users.length) {
        this.users = users
        console.log(this.users)
        this.loading = false
      }
    } catch (err) {
      console.log(err.response)
      this.loading = false
    }
  },
})
</script>

<style scoped>
.table td,
.table th {
  @apply border px-8 py-2 border-gray-400 text-center;
}
</style>
