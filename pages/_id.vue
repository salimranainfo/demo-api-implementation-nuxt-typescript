<template>
  <div class="h-full flex justify-center items-center">
    <main class="max-w-[1100px] mx-auto bg-white p-6 rounded-md shadow-md">
      <div v-if="!loading" class="mb-6">
        <nuxt-link
          to="/"
          class="font-bold px-4 py-2 border border-gray-400 rounded-md text-lg"
          >Back</nuxt-link
        >
      </div>
      <div v-if="user && !loading" class="table text-lg">
        <div class="row">
          <span class="font-bold">ID</span>
          <span>{{ user.id }}</span>
        </div>
        <div class="row">
          <span class="font-bold">Username</span>
          <span>{{ user.username }}</span>
        </div>
        <div class="row">
          <span class="font-bold">Name</span>
          <span>{{ user.name }}</span>
        </div>
        <div class="row">
          <span class="font-bold">Email</span>
          <span>{{ user.email }}</span>
        </div>
        <div class="row">
          <span class="font-bold">Phone</span>
          <span>{{ user.phone }}</span>
        </div>
        <div class="row">
          <span class="font-bold">Website</span>
          <span>{{ user.website }}</span>
        </div>
        <div class="row">
          <span class="font-bold">Company</span>
          <span class="pl-0">
            <span
              v-if="user.company"
              class="flex flex-col justify-start items-start w-full space-y-2"
            >
              <span class="font-bold inline-block w-full">{{
                user.company.name
              }}</span>
              <span class="inline-block w-full">{{
                user.company.catchPhrase
              }}</span>
              <span class="inline-block w-full">{{ user.company.bs }}</span>
            </span>
            <span v-else class="border-none"></span>
          </span>
        </div>

        <!-- Posts -->
        <div class="row">
          <span class="font-bold">Posts</span>
          <template v-if="posts.length">
            <span
              class="flex flex-col justify-start items-start w-full space-y-2"
            >
              <span v-for="post of posts" :key="post.id">
                <span>{{ post.id }} - {{ post.title }}</span>
              </span>
            </span>
          </template>
          <span v-else></span>
        </div>

        <!-- Albums -->
        <div class="row">
          <span class="font-bold">Albums</span>
          <template v-if="albums.length">
            <span
              class="flex flex-col justify-start items-start w-full space-y-2"
            >
              <span v-for="album of albums" :key="album.id">
                <span>{{ album.id }} - {{ album.title }}</span>
              </span>
            </span>
          </template>
          <span v-else></span>
        </div>

        <!-- Todos -->
        <div class="row">
          <span class="font-bold">Albums</span>
          <template v-if="todos.length">
            <span
              class="flex flex-col justify-start items-start w-full space-y-2"
            >
              <span
                v-for="todo of todos"
                :key="todo.id"
                class="flex flex-col justify-start items-start w-full space-y-1"
              >
                <span>{{ todo.id }} - {{ todo.title }}</span>
                <span
                  class="font-bold"
                  :class="todo.completed ? 'text-green-600' : 'text-red-600'"
                  >{{ todo.completed ? 'Done' : 'Pending' }}</span
                >
              </span>
            </span>
          </template>
          <span v-else></span>
        </div>
      </div>

      <div v-if="!user && !loading" class="font-bold">
        Could not fetch Users from database
      </div>

      <div v-if="loading" class="font-bold">Loading ....</div>
    </main>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

import User from '@/types/User'
import Post from '@/types/Post'
import Album from '@/types/Album'
import Todo from '@/types/Todo'

export default Vue.extend({
  data() {
    return {
      user: null as User | null,
      posts: [] as Post[],
      albums: [] as Album[],
      todos: [] as Todo[],
      loading: true,
    }
  },

  async fetch() {
    try {
      const user = await this.$axios.$get('/users/' + this.$route.params.id)

      if (user) {
        this.user = user

        const posts = await this.$axios.$get(
          '/posts?userId=' + this.$route.params.id
        )
        this.posts = posts

        const albums = await this.$axios.$get(
          '/albums?userId=' + this.$route.params.id
        )
        this.albums = albums

        const todos = await this.$axios.$get(
          '/todos?userId=' + this.$route.params.id
        )
        this.todos = todos

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
.row {
  @apply flex justify-between items-stretch;
}

.row > span {
  @apply w-full border-b border-gray-400 px-4 py-2;
}
</style>
