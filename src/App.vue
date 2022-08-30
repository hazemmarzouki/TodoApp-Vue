<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);

//add todo to list
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: new Date().getTime(),
    done: false,
  });
  input_content.value = "";
  input_category.value = null;
};

// a function to delete todo from list
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t.createdAt !== todo.createdAt);
};

// filter the todos list by ascending date created
const todos_asc = computed(() => {
  return todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  });
});

// change the name in the local storage and set it's value the new value of the name
watch(name, (val) => {
  localStorage.setItem("name", val);
});

//
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

// display the name in the local storage
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main clas="app">
    <section class="greeting">
      <h2 class="title">
        What's up ,
        <input type="text" v-model="name" placeholder="Your name here .." />
      </h2>
    </section>

    <section class="create-todo">
      <!-- the submit event will no longer reload the page -->
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list ?</h4>
        <input
          type="text"
          v-model="input_content"
          placeholder="e.g. make a potfolio website "
        />
        <h4>Pick a Category</h4>
        <div class="options">
          <label>
            <input type="radio" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

          <label>
            <input type="radio" value="learning" v-model="input_category" />
            <span class="bubble learning"></span>
            <div>Learning</div>
          </label>

          <label>
            <input type="radio" value="others" v-model="input_category" />
            <span class="bubble others"></span>
            <div>Others</div>
          </label>
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo-list">
      <h4>ToDo List :</h4>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
          :key="todo.createdAt">
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
