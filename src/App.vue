<script setup>
// ref: handle state
// watch: will check for any change on REF
// onMounted: execute code as soon as the page is render, it will bring the local storage values
// computed: will order the list by created date
import { ref, watch, onMounted, computed } from "vue";

const todos = ref([]); // will store the list of all todos
const name = ref(""); // user name

const input_content = ref(""); // the input field for the content of a todo
const input_category = ref(null); // the input field for the category of a todo

const todos_ascOrder = computed(
  () =>
    todos.value.sort((a, b) => {
      return b.createdAt - a.createdAt;
    }) // order the todos by createdAt date
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  // console.log(input_content.value.trim(), input_category.value);

  // create a object inside the todos array
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(), // using miliseconds do we can order by createdAt
  });

  input_content.value = "";
  input_category.value = null;
};

// we check the array for every item. If the item is different than the variable "todo", we put it back into the array
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

// if the variable "todos" changes on submit, we will store the newVal in the localstorage
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal)); //because it is an object we need to stringfy it
  },
  { deep: true } // because todos is an array, we need "deep" to look through the array
);

// if the variable "name" changes with the input, we will store the newVal in the localstorage
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// we bring the value on the localStorage to the input. whenever the screen is refreshed we have it
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name Here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a Todo</h3>
      <!-- Call the function addTodo to handle the submit -->
      <form @submit.prevent="addTodo">
        <h4>What's on your Todo list?</h4>
        <input
          type="text"
          placeholder="e.g. Make a Video"
          v-model="input_content"
        />
        <!-- {{ input_content }} -->
        <h4>Select a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
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
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

          <!-- {{ input_category }} -->
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <!-- {{ todos_ascOrder }} -->
    <section class="todo-list">
      <h4>Todo List</h4>
      <div class="list">
        <!-- v-for = vue for loop / for every todo inside todos_ascOrder -->
        <!-- create a class that display every item  -->
        <!-- check if the item is marked true for done. if so we display "done" -->
        <!-- set the class bubble to business or personal -->
        <!-- input will display the item and allow to be update -->
        <!-- the delete button will call function removeTodo to delete the item from localstorage -->
        <div
          v-for="todo in todos_ascOrder"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
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
