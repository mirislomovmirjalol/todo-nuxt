<template>
  <div class=" bg-gray-900 text-white h-screen text-center">
    <div class="container max-w-screen-sm mx-auto">
      <div class="mx-4">

        <h1 class="text-3xl font-medium py-10">ToDo App</h1>

        <form @submit.prevent="addTodo()">
          <div class="flex flex-col space-y-4">
            <div class="flex flex-col">
              <label class="text-left text-sm mb-2">New ToDo </label>
              <input
                  v-model="newTodo"
                  autocomplete="off"
                  class="py-3 px-4 bg-gray-800 border border-gray-500 rounded-md"
                  name="newTodo"
              />
            </div>
            <button
                class="py-3 bg-violet-300 text-black font-medium text-lg rounded-md hover:shadow-lg hover:shadow-violet-500/50 transition duration-300 ease-in-out"
                @click="addTodo()"
            >
              Add ToDo
            </button>
          </div>
        </form>

        <h2
            class="text-left my-4 text-xl font-medium border-b border-gray-500 pb-2"
        >
          ToDo List
        </h2>
        <client-only>
          <ul v-if="todos.length !== 0" class="mx-4">
            <TransitionGroup name="list" tag="ul">
              <li
                  v-for="(todo, index) in todos"
                  :key="todo"
                  class="text-sm py-4 px-4 my-2 border border-gray-500 rounded-md flex flex-row justify-between cursor-pointer"
                  @click="changeStatusTodo(todo)"
              >
                <span :class="{ done: todo.done }" @click="changeStatusTodo(todo)">{{ todo.content }}</span>
                <button
                    class="text-black font-medium text-xs p-1 rounded-md bg-violet-300"
                    @click="removeTodo(index)"
                >
                  Remove
                </button>
              </li>
            </TransitionGroup>
          </ul>
          <h4 v-else>No todos</h4>
        </client-only>
      </div>
    </div>
  </div>
</template>

<script setup>
import nuxtStorage from "nuxt-storage/nuxt-storage";

const newTodo = ref('')
const todoData = nuxtStorage.localStorage.getData('todosData') || []
const todos = await ref(todoData)

function addTodo() {
  if (newTodo.value) {
    todos.value.push({
      done: false,
      content: newTodo.value
    })
    newTodo.value = ''
  }
  saveTodo()
}

function changeStatusTodo(todo) {
  todo.done = !todo.done
  saveTodo()
}

function removeTodo(index) {
  todos.value.splice(index, 1)
  saveTodo()
}

function saveTodo() {
  nuxtStorage.localStorage.setData('todosData', todos.value);
}

useHead({
  title: 'ToDo App',
  meta: [
    {
      name: 'description',
      content: 'Nuxt 3 ToDo App with Composition API'
    }
  ]
})
</script>

<style scoped>
.done {
  @apply line-through;
}

.list-move, /* apply transition to moving elements */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: scaleX(0.5);
}

/* ensure leaving items are taken out of layout flow so that moving
   animations can be calculated correctly. */
.list-leave-active {
  position: absolute;
}
</style>
