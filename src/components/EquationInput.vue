<template>
  <div>
    <textarea
      name=""
      id=""
      cols="30"
      rows="10"
      :value="equation"
      @input="$emit('input', $event.target.value)"
      class="border-2 border-black"
    ></textarea>
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
