<template>
  <div>
    <SimpleDrag
      :responseData="response"
      :isModalOpened="modalOpened"
      addCardTitle="+Add Card"
      addSectionTitle="Add Section"
      @add-card="addNewCard"
      @edit-card="editData"
    >
      <template v-slot:cardForm>
      <input type="text" />
    </template>
    </SimpleDrag>
  </div>
</template>

<script setup>
import { ref } from "vue";
// import DragNDrop from 'drag-n-drop-vue';
import SimpleDrag from "./components/SimpleDrag.vue";

const response = ref({
  data: [
    {
      title: "Array 1",
      drop_col_id: "",
      data: [
        { html: "<p>AA<p>", age: 11 },
        { html: "<p>BB</p>", age: 22 },
        { html: "<p>CC</p>", age: 33 },
      ],
    },
    {
      title: "Array 2",
      data: [{ html: "<p>XX</p>", age: 66 }],
    },
    {
      title: "Array 3",
      data: [],
    },
  ],
});
const modalOpened = ref(false);
const addNewCard = () => {
};
const editData = () => {
  // console.log('edit data in app', event);
}
</script>

<style>
body {
  font-family: "Helvetica Neue", Arial, Helvetica, sans-serif;
  background: #80929b;
}

/* .wrapper {
  padding: 1rem;
} */
</style>
