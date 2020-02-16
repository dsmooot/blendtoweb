<template>
  <div id="app">
    <router-link to="/">Home</router-link>
    <router-link to="/HelloWorld">Next</router-link>
    <button @click="handleClick">Go Home</button>
    <router-view></router-view>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";
export default {
  data() {
    return {};
  },
  name: "App",
  components: {
    HelloWorld
  },
  methods: {
    handleClick() {
      this.$router.push("/");
    }
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
}
</style>
