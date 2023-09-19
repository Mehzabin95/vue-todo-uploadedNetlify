<script setup>
import TodoCreator from "../components/TodoCreator.vue";
import { uid } from "uid";
import { ref, computed } from "vue";
import { Icon } from "@iconify/vue";
import TodoItem from "../components/TodoItem.vue";

const todosList = ref([]);

const todoCompleted = computed(() => {
  return todosList.value.every((todo) => todo.isCompleted);
});
// ///////////////////////////(Saving todos in local storage)
const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todosList"));
  if (savedTodoList) {
    todosList.value = savedTodoList;
  }
};

// Fetch Todo's on page load
fetchTodoList();

const setTodoListLocalStorage = () => {
  localStorage.setItem("todosList", JSON.stringify(todosList.value));
};

// //////////////////////////
const createTodo = (todo) => {
  todosList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
  setTodoListLocalStorage();
};
const toggleTodoComplete = (todoPos) => {
  todosList.value[todoPos].isCompleted = !todosList.value[todoPos].isCompleted;
  setTodoListLocalStorage();
};

const toggleEditTodo = (todoPos) => {
  todosList.value[todoPos].isEditing = !todosList.value[todoPos].isEditing;
  setTodoListLocalStorage();
};

const UpdateTodo = (todoVal, todoPos) => {
  todosList.value[todoPos].todo = todoVal;
  setTodoListLocalStorage();
};

const DeleteTodo = (todoId) => {
  todosList.value = todosList.value.filter((todo) => todo.id !== todoId);
  setTodoListLocalStorage();
};
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todosList.length > 0">
      <TodoItem
        v-for="(todo, index) in todosList"
        :todo="todo"
        :index="index"
        @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo"
        @update-todo="UpdateTodo"
        @delete-todo="DeleteTodo"
      />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="clarity:sad-face-solid" color="orangered" width="22" />
      <span>You Have no todo's to complete! Add one</span>
    </p>
    <p v-if="todoCompleted && todosList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" color="orangered" width="22" />
      <span> You have completed all your todo's</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;
}
h1 {
  margin-bottom: 16px;
  text-align: center;
}

.todo-list {
  display: flex;
  flex-direction: column;
  list-style: none;
  margin-top: 24px;
  gap: 20px;
}

.todos-msg {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-top: 24px;
}
</style>
