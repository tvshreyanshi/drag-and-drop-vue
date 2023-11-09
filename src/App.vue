<template>
  <div>
    <SimpleDrag /> 
  </div>
</template>

<script>
import SimpleDrag from './components/SimpleDrag.vue';

export default {
  name: 'App',
  components: {
    SimpleDrag
  }
}
</script>

<style>
body {
 font-family: 'Helvetica Neue',Arial,Helvetica,sans-serif;
 background: #80929b;
}

/* .wrapper {
  padding: 1rem;
} */
</style>
