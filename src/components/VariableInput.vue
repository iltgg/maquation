<template>
  <div class="w-full flex">
    <span class="w-1/4 bg-gray-300">{{ variable.name }}</span>
    <input type="text" v-model="variable.value" class="w-3/4 bg-gray-300" />
  </div>
</template>

<script>
export default {
  props: {
    variable: Object,
  },
  data: function () {
    return {
      name: this.variable.name,
      value: this.variable.value,
    };
  },
};
</script>

<style></style>
