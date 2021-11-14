<template>
  <body class="flex justify-center items-center flex-col h-screen w-screen">
    <!-- Discription -->
    <div class="flex justify-center">
      <discription-input
        v-model="discription"
        v-show="edit"
      ></discription-input>
      <div
        v-html="markDown"
        v-show="!edit"
        class="no-tailwindcss w-96 border-2 border-black"
      ></div>
    </div>

    <!-- Equation -->
    <equation-input v-model="equation" v-show="edit"></equation-input>
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
      <input type="button" value="Evaluate" @click="mathEvaluate(true)" />
    </div>

    <!-- Options -->
    <div>
      <input type="checkbox" id="edit" value="true" v-model="edit" />
      <label for="edit">Edit mode</label>
      <input
        type="checkbox"
        id="auto"
        value="true"
        v-model="autoEvaluate"
        checked
      />
      <label for="auto">Auto evaluate equations</label>
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
  name: "App",
  data: function () {
    return {
      equation: "",
      solution: "waiting...",
      discription: "",
      edit: false,
      autoEvaluate: true,
    };
  },
  computed: {
    markDown: function () {
      return marked.parse(this.discription);
    },
    equationTex: function () {
      // Renders input equation as tex, also acts as validator
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
        // Traverses input equation to find variables
        math.parse(this.equation).traverse(function (node, path, parent) {
          // Checks if node is variable, sqrt sin etc. are also symbol nodes
          // but will be the child of a function node with the same name
          if (node.isSymbolNode && parent?.name !== node.name) {
            // Checks if variable has already been identified
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
        // Returns empty array on fail
        return variableArray;
      }
      return variableArray;
    },
  },
  methods: {
    mathEvaluate: function (override) {
      if (this.autoEvaluate || override) {
        const scope = {};
        this.varList.forEach((variable) => {
          scope[variable.name] = math.evaluate(
            // math.evalute so any input can be parsed
            variable.value ? variable.value : 0 // If no value then default to 0
          );
        });
        this.solution = math.evaluate(this.equation, scope);
      }
    },
  },
  components: {
    VariableInput,
    EquationInput,
    DiscriptionInput,
  },
  updated: function () {
    // import auto katex for real later
    window.renderMathInElement(document.body, {
      // customised options
      // auto-render specific keys, e.g.:
      delimiters: [
        { left: "$$", right: "$$", display: true },
        { left: "$", right: "$", display: false },
        { left: "\\(", right: "\\)", display: false },
        { left: "\\[", right: "\\]", display: true },
      ],
      // rendering keys, e.g.:
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
