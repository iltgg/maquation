<template>
  <div>
    <input
      :value="equation"
      @input="$emit('input', $event.target.value)"
      class="border-2 border-black"
    />
  </div>
</template>

<script>
export default {
  props: {
    equation: String,
  },
};
</script>

<style></style>
