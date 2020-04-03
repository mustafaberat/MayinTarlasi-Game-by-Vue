<template>
  <div id="app">
    <MayinTarlasi />
  </div>
</template>

<script>
import MayinTarlasi from "./components/MayinTarlasi.vue";

export default {
  name: "App",
  components: {
    MayinTarlasi
  }
};
</script>

<style lang="scss">
@import "./styles/main.scss";
</style>
