<template>
  <section>
    <h1>Todos</h1>
    <todos-filter></todos-filter>
    <ul>
      <li v-for="(todo, idx) in todos" :key="todo._id">
      <button>&uarr;</button>
      <button>&darr;</button>
        {{idx}}.
        <input type="checkbox" :checked="todo.completed" @change="toggleTodo(todo)"> {{todo.txt}}
        <h6>
          <button :disabled="todo.importance >= 3" @click="updateImportance(todo, 1)">+</button>
          Importance: {{todo.importance}}
          <button :disabled="todo.importance <= 1" @click="updateImportance(todo, -1)">-</button>

        </h6>

      </li>
      <form @submit.prevent="addTodo">
        <input type="text" v-model="newTodo.txt">
        <button>Add</button>
      </form>

    </ul>

  </section>
</template>

<script>


import { TODO_CREATE, TODO_UPDATE, TODO_LOAD } from '../store/todos.store'

import todoService from '../services/todo.service'
import TodosFilter from './TodosFilter'

export default {
  data() {
    return {
      newTodo: todoService.emptyTodo()
    }
  },
  created() {
    this.$store.dispatch({ type: TODO_LOAD });
  },
  computed: {
    todos() {
      return this.$store.getters.filteredTodos
    }
  },
  methods: {
    toggleTodo(todo) {
      const todoUpdated = Object.assign({},
        todo,
        { completed: !todo.completed }
      )
      this.$store.dispatch({ type: TODO_UPDATE, todo: todoUpdated });
    },
    addTodo() {
      this.$store.dispatch({ type: TODO_CREATE, todo: this.newTodo });
      this.newTodo = todoService.emptyTodo();
    },
    updateImportance(todo, diff) {
      const todoUpdated = Object.assign({},
        todo,
        { importance: todo.importance + diff }
      )
      this.$store.dispatch({ type: TODO_UPDATE, todo: todoUpdated });
    }

  },
  components: {
    TodosFilter
  }
}
</script>


<style scoped>
section {
  text-align: initial;
}

li {
  list-style: none;
  display: block;
  padding: 10px;
  border-bottom: 1px solid lightgray;
}

button[disabled] {
  background-color: white;
  cursor: not-allowed;
}
</style>
