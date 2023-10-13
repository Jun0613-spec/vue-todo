<script setup>
import { ref, onMounted, computed, watch } from "vue";

const grocerys = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const grocerys_asc = computed(() =>
  grocerys.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  grocerys,
  (newVal) => {
    localStorage.setItem("grocerys", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addgrocery = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  grocerys.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

const removegrocery = (grocery) => {
  grocerys.value = grocerys.value.filter((t) => t !== grocery);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  grocerys.value = JSON.parse(localStorage.getItem("grocerys")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up!
        <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-grocery">
      <h3>CREATE GROCERY LIST</h3>

      <form id="new-grocery-form" @submit.prevent="addgrocery">
        <h4>What's on your grocery list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. Eggs"
          v-model="input_content"
        />

        <h4>Pick a category</h4>
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
            <div>Week day</div>
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
            <div>Weekend</div>
          </label>
        </div>

        <input type="submit" value="Add" />
      </form>
    </section>

    <section class="grocery-list">
      <h3>GROCERY LIST</h3>
      <div class="list" id="grocery-list">
        <div
          v-for="grocery in grocerys_asc"
          :class="`grocery-item ${grocery.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="grocery.done" />
            <span
              :class="`bubble ${
                grocery.category == 'business' ? 'business' : 'personal'
              }`"
            ></span>
          </label>

          <div class="grocery-content">
            <input type="text" v-model="grocery.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removegrocery(grocery)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
