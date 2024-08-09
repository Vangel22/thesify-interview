<template>
  <div class="w-full flex justify-center items-center">
    <div class="w-1/2 rounded flex justify-center m-10 p-10 bg-orange-300">
      <span class="text-lg">{{ msg }}</span>
    </div>
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "NoData",
  props: {
    msg: String,
  },
});
</script>
