<template>
  <body class="flex justify-center items-center flex-col h-screen w-screen">
    <div class="flex justify-center">
      <discription-input v-model="discription"></discription-input>
      <div
        v-html="markDown"
        class="no-tailwindcss w-96 border-2 border-black"
      ></div>
    </div>

    <equation-input v-model="equation"></equation-input>
    <div v-html="equationTex"></div>

    <div class="bg-pink-300 text-black dark:bg-gray-700 dark:text-white">
      {{ solution }}
    </div>

    <!-- List all Variables + input -->
    <div class="border-2 border-black p-4">
      <div v-for="variable in varList" :key="variable.name">
        <variable-input
          :variable="variable"
          @evaluate="mathEvaluate"
        ></variable-input>
      </div>
    </div>
  </body>
</template>

<script>
import VariableInput from "./components/VariableInput.vue";
import EquationInput from "./components/EquationInput.vue";
import DiscriptionInput from "./components/DiscriptionInput.vue";
import { create, all } from "mathjs";
import { marked } from "marked";
import katex from "katex";

const math = create(all);

class Variable {
  constructor(name, value) {
    this.name = name;
    this.value = value ? value : 0;
  }
}

export default {
  data: function () {
    return {
      equation: "",
      solution: "waiting...",
      discription: "",
      edit: false,
    };
  },
  computed: {
    markDown: function () {
      return marked.parse(this.discription);
    },
    equationTex: function () {
      try {
        let tex = math.parse(this.equation).toTex();
        if (tex !== "undefined") {
          return katex.renderToString(tex, { throwOnError: false });
        }
      } catch (error) {
        return error.message;
      }
      return "Enter Equation";
    },
    varList: function () {
      let variableArray = [];
      try {
        math.parse(this.equation).traverse(function (node, path, parent) {
          if (node.isSymbolNode && parent?.name !== node.name) {
            if (
              variableArray.filter((v) => {
                return v.name === node.name;
              }).length === 0
            ) {
              variableArray.push(new Variable(node.name));
            }
          }
        });
      } catch {
        return variableArray;
      }
      return variableArray;
    },
  },
  methods: {
    mathEvaluate: function () {
      const scope = {};
      this.varList.forEach((variable) => {
        scope[variable.name] = math.number(
          math.fraction(variable.value ? variable.value : 0)
        ); // Allows fraction answers
      });
      this.solution = math.evaluate(this.equation, scope);
    },
  },
  components: {
    VariableInput,
    EquationInput,
    DiscriptionInput,
  },
  updated: function () {
    // Cheese because idk how to import katex with npm
    window.renderMathInElement(document.body, {
      // customised options
      // • auto-render specific keys, e.g.:
      delimiters: [
        { left: "$$", right: "$$", display: true },
        { left: "$", right: "$", display: false },
        { left: "\\(", right: "\\)", display: false },
        { left: "\\[", right: "\\]", display: true },
      ],
      // • rendering keys, e.g.:
      throwOnError: false,
    });
  },
};
</script>

<style lang="scss">
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type="number"] {
  -moz-appearance: textfield;
}
</style>
