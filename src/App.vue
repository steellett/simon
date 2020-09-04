<template>
  <div id="app">
    <simon></simon>
  </div>
</template>

<script>
import simon from "./components/simon.vue";
export default {
  name: "App",
  components: {
    simon,
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
