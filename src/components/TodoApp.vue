<script setup lang="ts">
import { ref, onMounted, watch } from "vue"
import { marked } from "marked"

type Todo = {
  id: string
  title: string
  content: string
  isOpen: boolean
}

const todos = ref<Todo[]>([])
const newTitle = ref("")

onMounted(() => {
  const saved = localStorage.getItem("todos")
  if (saved) todos.value = JSON.parse(saved)
})

watch(todos, () => {
  localStorage.setItem("todos", JSON.stringify(todos.value))
}, { deep: true })

const addTodo = () => {
  if (!newTitle.value) return
  todos.value.push({
    id: crypto.randomUUID(),
    title: newTitle.value,
    content: "",
    isOpen: false
  })
  newTitle.value = ""
}
</script>

<template>
  <div>
    <h2>Todo</h2>

    <input v-model="newTitle" placeholder="Todo title" />
    <button @click="addTodo">追加</button>

    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <h3>
          <div style="display:flex">{{ todo.title }} <button @click="todo.isOpen = !todo.isOpen" style="margin-left: 10px; cursor:pointer">Edit</button></div>
        </h3>

        <div v-if="todo.isOpen">
          <textarea v-model="todo.content" rows="6" placeholder="Markdown を入力" />
        </div>
        <div v-if="!todo.isOpen">
          <div v-html="marked(todo.content)" />
        </div>
      </li>
    </ul>
  </div>
</template>