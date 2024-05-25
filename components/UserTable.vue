<template>
  <div v-if="paginatedUsers.length" class="user-table">
    <table class="user-table__table">
      <thead class="user-table__thead">
        <tr class="user-table__row">
          <th class="user-table__header">Name</th>
          <th class="user-table__header">Email</th>
          <th class="user-table__header">Phone</th>
          <th class="user-table__header">Username</th>
        </tr>
      </thead>
      <tbody class="user-table__tbody">
        <tr
          class="user-table__row"
          v-for="user in paginatedUsers"
          :key="user.id"
        >
          <td
            class="user-table__cell user-table__cell--name"
            @click="goToTodoList(user.id)"
          >
            {{ user.name }}
          </td>
          <td class="user-table__cell">{{ user.email }}</td>
          <td class="user-table__cell">{{ user.phone }}</td>
          <td class="user-table__cell">{{ user.username }}</td>
        </tr>
      </tbody>
    </table>

    <div class="user-table__pagination">
      <button
        class="user-table__button"
        :disabled="currentPage === 1"
        @click="prevPage"
      >
        Prev
      </button>
      <span class="user-table__page-info">
        Page {{ currentPage }} of {{ totalPages }}
      </span>
      <button
        class="user-table__button"
        :disabled="currentPage === totalPages"
        @click="nextPage"
      >
        Next
      </button>
    </div>
  </div>
  <div v-else>Loading...</div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";

interface User {
  id: number;
  name: string;
  email: string;
  phone: string;
  username: string;
}

const users = ref<User[]>([]);
const itemsPerPage = 5;
const currentPage = ref(1);
const router = useRouter();

onMounted(async () => {
  loadPage();

  try {
    const response = await axios.get(
      "https://jsonplaceholder.typicode.com/users"
    );
    users.value = response.data;
  } catch (error) {
    console.error("Error fetching users:", error);
  }
});

const paginatedUsers = computed(() => {
  const startIndex = (currentPage.value - 1) * itemsPerPage;
  const endIndex = startIndex + itemsPerPage;
  return users.value.slice(startIndex, endIndex);
});

const totalPages = computed(() => Math.ceil(users.value.length / itemsPerPage));

function prevPage() {
  currentPage.value--;
  savePage();
}

function nextPage() {
  currentPage.value++;
  savePage();
}
function savePage() {
  localStorage.setItem("currentPage", currentPage.value.toString());
}
function loadPage() {
  const storedPage = localStorage.getItem("currentPage");
  currentPage.value = storedPage ? parseInt(storedPage) : 1;
}

const goToTodoList = (userId: number) => {
  router.push(`/todos/${userId}`);
};
</script>

<style scoped lang="scss">
.user-table {
  &__table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }

  &__thead {
    background-color: #f2f2f2;
  }

  &__row:nth-child(even) {
    background-color: #f9f9f9;
  }

  &__row:hover {
    background-color: #f1f1f1;
  }

  &__header,
  &__cell {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: left;
  }

  &__cell--name {
    cursor: pointer;
    color: #007bff;
    text-decoration: underline;

    &:hover {
      color: #0056b3;
    }
  }

  &__pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 20px;
  }

  &__button {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
    margin: 0 5px;

    &:disabled {
      background-color: #cccccc;
      cursor: not-allowed;
    }

    &:hover:not(:disabled) {
      background-color: #0056b3;
    }
  }

  &__page-info {
    margin: 0 10px;
  }
}
</style>
