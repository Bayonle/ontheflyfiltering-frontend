<template>
  <div>
    <input
      class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
      id="grid-zip"
      type="text"
      :value="modelValue"
      @input="handleInput($event)"
      v-bind="$attrs"
    />
  </div>
</template>

<script>
/* eslint-disable no-debugger */
/* eslint-disable vue/no-unused-components */
/* eslint-disable no-unused-vars */

// import { ValidationProvider } from 'vee-validate';

export default {
    components: {
        // ValidationProvider
    },
  inheritAttrs: false,

  props: {
    modelValue: {
      type: String
    }
  },
  methods: {
    handleInput(e) {
      this.$emit("update:modelValue", e.target.value);
    }
  }
};
</script>