<template>
  <div>
    <textarea
      name=""
      id=""
      cols="30"
      rows="10"
      :value="discription"
      @input="$emit('input', $event.target.value)"
      class="border-2 border-black"
    ></textarea>
  </div>
</template>

<script>
export default {
  props: {
    discription: String,
  },
};
</script>

<style></style>
