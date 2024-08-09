<template>
  <div class="w-full flex flex-col p-10 justify-center items-center">
    <h1 class="py-20 font-bold text-4xl">Hello world</h1>
    <p class="text-center">
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Nostrum dolor
      perspiciatis odit repudiandae, qui omnis eum quae accusantium tempore
      illo, excepturi nulla eius praesentium deserunt ad, architecto eligendi
      libero quisquam!
    </p>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "HelloWorld",
  props: {
    msg: String,
  },
});
</script>
