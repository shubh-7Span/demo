<!-- App.vue -->
<template>
  <div id="app">
    <h1>Example 2</h1>

    <Example1 />


    <h1>Example 2 (Twubric)</h1>
    <Example2 />

  </div>
</template>

<script>
import Example1 from './components/EXample1.vue';
import Example2 from './components/Example2.vue'


export default {
  name: 'App',
  components: {
    Example1,

    Example2
  }
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>