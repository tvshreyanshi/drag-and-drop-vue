<template>
  <div class="container">
    <p>Darg & Drop</p>
    <h5>Array1</h5>  
    <ul v-for="(data, index) in Array1" :key="index">
      <li>
        {{ data }}
      </li>
    </ul>
    <h5>Array2</h5>
    <ul v-for="(data, index) in Array2" :key="index">
      <li>{{ data }}</li>
    </ul>
  </div>
</template>
<script>
import { ref } from "vue";
export default {
  name: "SimpleDrag",
  setup() {
    const Array1 = ref(["Item1", "Item2", "Item3", "Item4"]);
    let Array2 = ref([]);

    return {
      Array1,
      Array2,
    };
  },
};
</script>