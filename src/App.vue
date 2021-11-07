<template>
  <body class="flex justify-center items-center flex-col h-screen w-screen">
    <discription-input v-model="discription"></discription-input>
    <div v-html="markDown" class="no-tailwindcss"></div>
    <equation-input v-model="equation"></equation-input>
    <div class="bg-pink-300 text-black dark:bg-gray-700 dark:text-white">
      <ul>
        <li v-for="variable in varList" :key="variable.name">
          {{ variable.name }}, {{ variable.value }}
        </li>
        <li>
          {{ solution }}
        </li>
      </ul>
    </div>

    <div class="border-2 border-black p-4">
      <div v-for="variable in varList" :key="variable.name">
        <variable-input
          :variable="variable"
          @evaluate="mathEvaluate"
        ></variable-input>
      </div>
    </div>
    <div class="flex flex-col items-center">
      <div>
        <input type="text" v-model="newVarName" />
        <input type="button" value="Add" @click="addVariable" />
      </div>
      <div>
        <input type="button" value="Evaluate" @click="mathEvaluate" />
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
      varList: [new Variable("a", 23)],
      equation: "",
      solution: "",
      newVarName: "",
      discription: "",
    };
  },
  computed: {
    markDown: function () {
      return marked.parse(this.discription);
    },
  },
  methods: {
    addVariable: function () {
      let isError = false;
      if (this.newVarName !== "") {
        for (let i = 0; i < this.varList.length; i++) {
          if (this.varList[i].name === this.newVarName) {
            isError = true;
            alert(`Variable ${this.newVarname} name already in use`);
            break;
          }
        }
      } else {
        isError = true;
        alert("Invalid variable name");
      }

      if (!isError) {
        this.varList.push(new Variable(this.newVarName));
        this.newVarName = "";
      }
    },
    mathEvaluate: function () {
      const scope = {};
      this.varList.forEach((variable) => {
        scope[variable.name] = variable.value;
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
