<template>
  <div>
    <div class="todo-list" v-if="todos.length">
      <h2 class="todo-list__title">{{ userName }}'s Todo List</h2>
      <ul class="todo-list__items">
        <li class="todo-list__item" v-for="todo in todos" :key="todo.id">
          <input
            class="todo-list__checkbox"
            type="checkbox"
            :checked="todo.completed"
            disabled
          />
          <span class="todo-list__text">{{ todo.title }}</span>
        </li>
      </ul>
      <button class="todo-list__button" @click="goBack">
        Back to Users List
      </button>
    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import axios from "axios";

interface Todo {
  id: number;
  completed: boolean;
  title: string;
}

const route = useRoute();
const router = useRouter();
const userId = route.params.userId;
const userName = ref("");
const todos = ref<Todo[]>([]);

onMounted(async () => {
  try {
    const userResponse = await axios.get(
      `https://jsonplaceholder.typicode.com/users/${userId}`
    );
    userName.value = userResponse.data.name;

    const todosResponse = await axios.get(
      `https://jsonplaceholder.typicode.com/todos?userId=${userId}`
    );
    todos.value = todosResponse.data;
  } catch (error) {
    console.error("Error fetching data:", error);
  }
});

function goBack() {
  router.push("/");
}
</script>

<style scoped lang="scss">
.todo-list {
  &__title {
    font-size: 24px;
    margin-bottom: 20px;
  }

  &__items {
    list-style-type: none;
    padding: 0;
  }

  &__item {
    display: flex;
    align-items: center;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    margin-bottom: 10px;
    background-color: #f9f9f9;

    &:hover {
      background-color: #f1f1f1;
    }
  }

  &__checkbox {
    margin-right: 10px;
  }

  &__text {
    font-size: 16px;
  }

  &__button {
    display: inline-block;
    margin-top: 20px;
    padding: 10px 20px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    text-align: center;

    &:hover {
      background-color: #0056b3;
    }
  }
}
</style>
