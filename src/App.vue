<template>
  <div id="app">
    <notes></notes>
  </div>
</template>

<script>
import myNotebook from './components/notebook.vue';

export default {
  name: 'App',
  components: {
    notes: myNotebook,
    
  }
}
</script>

<style>
.Mob{
    border: 2px double black;
    border-radius: 20px;
    text-align: center;
    margin-top: 5%;
    margin-left: 30%;
    margin-right: 30%;
    height: 1000px;
    background-color: rgb(26, 27, 26);
    color: rgb(238, 228, 228);
}
</style>
