<template>
  <div class="container d-flex">
    <div
      class="card shadow-lg p-3 mb-5 bg-body rounded m-2"
      style="width: 18rem"
      v-for="(data, index) in response.data"
      :key="index"
    >
      <h5 class="card-title">{{data.title}}</h5>
      <div class="h-100">
        <ul
          @drop.prevent="dropItem(index, $event)"
          @dragover.prevent="allowDrop"
          class="list-group list-group-flush h-100"
        >
          <li
            class="list-group-item"
            v-for="(item, itemindex) in data.data"
            :key="itemindex"
            :draggable="true"
            @dragstart="dragStart(index, itemindex)"
            @dragover.prevent="internalAllowDrop"
            @drop.prevent="internalDrop(index, itemindex)"
          >
            {{ item.name }}
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
    const response = ref({"data": [
          {
            title:'Array 1',
            data:[{ name: "AA", age: 11 }, { name: "BB", age: 22 }, { name: "CC", age: 33 },]
          },
          {
            title: 'Array 2',
            data:[{ name: "XX", age: 66 }],
          },
          {
            title: 'Array 3',
            data: []
          }
    ] })
    const draggedItem = ref(null);
    const draggedArray = ref(null);

    const dragStart = (arrayIndex, itemIndex) => {
      draggedItem.value = itemIndex;
      draggedArray.value = arrayIndex;
    };
    const allowDrop = (event) => {
      event.preventDefault();
    };
    const internalAllowDrop = (event) => {
      event.preventDefault();
    };
    const internalDrop = (targetArrayIndex, targetIndex) => {
      if (draggedItem.value === null || draggedArray.value === null) return;

      const targetArray = response.value.data[targetArrayIndex];
      const draggedItemObj = targetArray.data.splice(draggedItem.value, 1)[0];
      targetArray.data.splice(targetIndex, 0, draggedItemObj);

      draggedItem.value = null;
      draggedArray.value = null;
    };
    const dropItem = (targetArrayIndex, event) => {
      if (draggedItem.value === null || draggedArray.value === null) return;

      const targetArray = response.value.data[targetArrayIndex];
      const targetList = event.target.parentElement;
      const targetIndex = Array.from(targetList.children).indexOf(event.target);
      if (targetIndex === targetList.children.length - 1) {
        const sourceArrayIndex = draggedArray.value;
        if (sourceArrayIndex !== targetArrayIndex) {
          const sourceArray = response.value.data[sourceArrayIndex];
          targetArray.data.push(sourceArray.data[draggedItem.value]);
          sourceArray.data.splice(draggedItem.value, 1);
        }
      } else {
        const sourceArrayIndex = draggedArray.value;
        if (sourceArrayIndex !== targetArrayIndex) {
          const sourceArray = response.value.data[sourceArrayIndex];
          targetArray.data.splice(targetIndex, 0, sourceArray.data[draggedItem.value]);
          sourceArray.data.splice(draggedItem.value, 1);
        }
      }

      draggedItem.value = null;
      draggedArray.value = null;
    };
    return {
      response,
      dragStart,
      allowDrop,
      dropItem,
      internalDrop,
      internalAllowDrop,
    };
  },
};
</script>