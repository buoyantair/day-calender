<template>
  <div id="app">
    <Calender />
  </div>
</template>

<script>
import Calender from "./components/Calender.vue";
export default {
  name: "app",
  components: {
    Calender
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  height: 100vh;
  width: 100vw;
  padding: 0;
  margin: 0;
}
</style>
