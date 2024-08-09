<template>
  <HelloWorld />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import "./index.css";
import HelloWorld from "./components/HelloWorld.vue";

export default defineComponent({
  name: "App",
  components: {
    HelloWorld,
  },
});
</script>
