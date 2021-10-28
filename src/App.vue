<template>
  <body class="flex justify-center items-center h-screen w-screen">
    <div
      class="bg-pink-300 text-black dark:bg-gray-700 dark:text-white w-8 h-8"
    >
      {{ varList[0].name }}
    </div>
  </body>
</template>

<script>
class Variable {
  constructor(name, value) {
    this.name = name;
    this.value = value ? value : 0;
  }
}

export default {
  data: function () {
    return {
      varList: [new Variable("a", 23)],
    };
  },
  methods: {},
};
</script>

<style></style>
