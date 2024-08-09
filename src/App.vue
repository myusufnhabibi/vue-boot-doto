<script setup>
import { computed, onMounted, ref, watch } from "vue";

const name = ref();
const todos = ref([]);

const i_title = ref("");
const i_category = ref(null);

const todo_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const handleForm = () => {
  if (i_title.value.trim === "" || i_category.value == null) {
    return;
  }

  todos.value.push({
    title: i_title.value,
    category: i_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });

  i_title.value = "";
  i_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name");
  todos.value = JSON.parse(localStorage.getItem("todos"));
});
</script>

<template>
  <div class="main p-4">
    <h2>
      What's Up,
      <input
        id="textInput"
        type="text"
        placeholder="Name here"
        v-model="name"
      />
    </h2>

    <section class="form-todo">
      <h6 class="mt-4">CREATE TODO</h6>
      <p class="text-muted">What's on your todo list</p>
      <form @submit.prevent="handleForm">
        <input
          type="text"
          v-model="i_title"
          class="form-control"
          placeholder="e.g Jogging"
        />
        <div class="row mt-2">
          <div class="col-6">
            <div class="card border-primary p-2">
              <input type="radio" v-model="i_category" value="business" />
              <label class="text-center" for="">Business</label>
            </div>
          </div>
          <div class="col-6">
            <div class="card border-success p-2">
              <input type="radio" v-model="i_category" value="personal" />
              <label class="text-center" for="">Personal</label>
            </div>
          </div>
        </div>
        <div class="d-grid mt-2">
          <button type="submit" class="btn btn-small btn-primary">
            Add Todo
          </button>
        </div>
      </form>
    </section>

    <section class="mt-4">
      <h6>TODO LIST</h6>
      <div
        :class="`card p-2 mb-2 ${
          todo.category == 'business' ? 'border-primary' : 'border-success'
        }`"
        v-for="todo in todo_asc"
        :key="todo.id"
      >
        <div class="form-check">
          <input type="checkbox" class="form-check-input" v-model="todo.done" />
          <label
            class="form-check-label"
            :style="` ${todo.done && 'text-decoration: line-through'}`"
            >{{ todo.title }}</label
          >
        </div>
        <button
          @click="removeTodo(todo)"
          class="btn btn-danger btn-sm float-right"
        >
          Delete
        </button>
      </div>
    </section>
  </div>
</template>

<style scoped>
#textInput {
  border: 0px;
}

#textInput:focus {
  outline: none;
}
</style>
