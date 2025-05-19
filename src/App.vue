<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => {
  return [...todos.value].sort((a, b) => b.createdAt - a.createdAt)
})

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

// Watch and save to localStorage
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// Load from localStorage
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">

    <!-- Greeting -->
    <section class="greeting">
      <h2 class="title">
        What's up, 
        <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <!-- Create Todo -->
    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text" 
          placeholder="e.g. make a coding app"
          v-model="input_content"
        />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <!-- Todo List -->
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div 
          v-for="todo in todos_asc" 
          :key="todo.createdAt"
          :class="['todo-item', { done: todo.done }]">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="['bubble', todo.category]"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>
</template>

<style scoped>
.app {
  padding: 2rem;
  font-family: Arial, sans-serif;
}

input[type="text"] {
  margin-top: 10px;
  padding: 0.5rem;
  width: 100%;
  font-size: 1rem;
}

input[type="submit"] {
  margin-top: 15px;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
}

.todo-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ddd;
}

.todo-item.done input[type="text"] {
  text-decoration: line-through;
  color: #aaa;
}

.bubble {
  display: inline-block;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  margin-right: 8px;
}

.business {
  background-color: blue;
}

.personal {
  background-color: orange;
}

.actions .delete {
  background-color: red;
  border: none;
  color: white;
  padding: 0.3rem 0.8rem;
  cursor: pointer;
}
</style>

