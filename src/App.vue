<template>
  <div class="container text-left">
    <h1 class="text-center mt-3">Todo List ({{ unfinishedTodos.length }} unfinished tasks)</h1>
    <Modal v-model="closeableModal" closeable header="Closeable Modal">
      <form v-if="editingTodo" @submit.prevent="saveEdit">
        <input v-model="editingTodo.title" type="text" class="col-10 col-xl-11 border-right-0" placeholder="Update todo...">
        <button type="submit" class="col-2 col-xl-1 bg-primary text-white rounded-right d-flex align-items-center justify-content-center m-0 p-0 pointer">Update</button>
      </form>
      <p v-else>This modal</p>
    </Modal>
    <div class="col-10 offset-1 col-md-8 offset-md-2 col-xl-6 offset-xl-3 p-0 my-4">
      <div class="d-flex">
        <input v-model="newTodo" @keyup.enter="onEnter" name="newTodo" type="text" class="col-10 col-xl-11 border-right-0" placeholder="Add a new todo...">
        <button @click.prevent="addNewTodo" class="col-2 col-xl-1 bg-primary text-white rounded-right d-flex align-items-center justify-content-center m-0 p-0 pointer">Add</button>
      </div>
      <b-list-group>
        <b-list-group-item
          class="d-flex justify-content-between align-items-center"
          v-for="(todo, index) in todos"
          :key="todo.id"
          :class="{ 'is-done': todo.done }"
        >
          <span>{{ todo.title }}</span> 
          <button
            class="btn"
            :class="btnClass(todo)"
            :title="'set ' + (todo.done ? 'todo' : 'done')"
            @click="toggleDone(todo)"
          >
            {{ todo.done ? "✓" : "✗" }}
          </button>
          <button class="btn" @click="editTodo(todo)" :disabled="todo.done">✏️</button> 
          <button class="btn" @click="deleteTodo(index)">❌</button> 
        </b-list-group-item>
      </b-list-group>
    </div>
  </div>
 </template>
 
 <script>
 import { ref, computed } from 'vue';
 import Modal from '@/components/EdithModal.vue'
 
 export default {
  components: {
    Modal,
  },
  setup() {
    const newTodo = ref('');
    const todos = ref([]);
    const closeableModal = ref(false);
    const editingTodo = ref(null);
    let todoIdTracker = 0;
 
    const addNewTodo = () => {
    if (newTodo.value.trim() === '') {
      alert('Please enter a new todo.');
      return;
    }
    todos.value.push({
      id: todoIdTracker,
      done: false,
      title: newTodo.value,
    });
    newTodo.value = '';
    todoIdTracker++;
    }

 
    const editTodo = (todo) => {
      editingTodo.value = todo;
      closeableModal.value = true;
    }
 
    const saveEdit = () => {
    const index = todos.value.findIndex(todo => todo.id === editingTodo.value.id);
    if (index !== -1) {
      todos.value[index] = editingTodo.value;
    }
    closeableModal.value = false;
    editingTodo.value = null;
    }

 
    const deleteTodo = (index) => {
      todos.value.splice(index, 1);
    }
 
    const toggleDone = (todo) => {
      todo.done = !todo.done;
    }
 
    const onEnter = () => {
      addNewTodo();
    }
 
    const btnClass = (todo) => {
      return todo.done ? "btn-success" : "btn-primary";
    }
 
    const unfinishedTodos = computed(() => todos.value.filter(todo => !todo.done));
 
    return {
      newTodo,
      todos,
      addNewTodo,
      toggleDone,
      deleteTodo,
      onEnter,
      btnClass,
      closeableModal,
      editTodo,
      saveEdit,
      editingTodo,
      unfinishedTodos,
    }
  }
 }
 </script>
 
 <style>
 .pointer {
  cursor: pointer;
 }
 
 .done {
  text-decoration: line-through;
 }
 </style>
 