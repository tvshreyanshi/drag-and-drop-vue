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
}

/* .wrapper {
  padding: 1rem;
} */
</style>
