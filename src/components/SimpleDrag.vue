<template>
  <div class="container d-flex">
    <div
      class="card shadow-lg p-3 mb-5 bg-body rounded m-2"
      style="width: 18rem"
    >
      <h5 class="card-title">Array1</h5>
      <div class="h-100">
        <ul
          @drop.prevent="dropItem('array1', $event)"
          @dragover.prevent="allowDrop"
          class="list-group list-group-flush h-100"
        >
          <li
            class="list-group-item"
            v-for="(data, index) in array1"
            :key="index"
            :draggable="true"
            @dragstart="dragStart('array1', index)"
          >
            {{ data }}
          </li>
        </ul>
      </div>
    </div>
    <div
      class="card shadow-lg p-3 mb-5 bg-body rounded m-2"
      style="width: 18rem"
    >
      <h5 class="card-title">Array2</h5>
      <div class="h-100">
        <ul
          @drop.prevent="dropItem('array2', $event)"
          @dragover.prevent="allowDrop"
          class="list-group list-group-flush h-100"
        >
          <li
            v-for="(data, index) in array2"
            :key="index"
            class="list-group-item"
            draggable="true"
            @dragstart="dragStart('array2', index)"
          >
            {{ data }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import { ref } from "vue";
export default {
  name: "SimpleDrag",
  setup() {
    const array1 = ref(["Item 1", "Item 2", "Item 3"]);
    const array2 = ref(["Item 4", "Item 5", "Item 6"]);
    const draggedItem = ref(null);
    const draggedArray = ref(null);

    const dragStart = (arrayName, index) => {
      draggedItem.value = index;
      draggedArray.value = arrayName;
    };
    const allowDrop = (event) => {
      event.preventDefault();
    };
    const dropItem = (targetArray, event) => {
      if (draggedItem.value === null || draggedArray.value === null) return;

      const targetList = event.target.parentElement;
      const targetIndex = Array.from(targetList.children).indexOf(event.target);
      if (targetIndex === targetList.children.length - 1) {
        if (targetArray === "array1" && draggedArray.value === "array2") {
          array1.value.push(array2.value[draggedItem.value]);
          array2.value.splice(draggedItem.value, 1);
        } else if (
          targetArray === "array2" &&
          draggedArray.value === "array1"
        ) {
          array2.value.push(array1.value[draggedItem.value]);
          array1.value.splice(draggedItem.value, 1);
        }
      } else {
        if (targetArray === "array1" && draggedArray.value === "array2") {
          array1.value.splice(targetIndex, 0, array2.value[draggedItem.value]);
          array2.value.splice(draggedItem.value, 1);
        } else if (
          targetArray === "array2" &&
          draggedArray.value === "array1"
        ) {
          array2.value.splice(targetIndex, 0, array1.value[draggedItem.value]);
          array1.value.splice(draggedItem.value, 1);
        }
      }

      draggedItem.value = null;
      draggedArray.value = null;
    };
    return {
      array1,
      array2,
      dragStart,
      allowDrop,
      dropItem,
    };
  },
};
</script>