<template>
  <div id="app">
    <restaurantes></restaurantes>
  </div>
</template>

<script>
import Restaurantes from "./components/Restaurantes.vue";
export default {
  name: "app",
  components: { Restaurantes },
  data() {
    return {};
  }
};
</script>
