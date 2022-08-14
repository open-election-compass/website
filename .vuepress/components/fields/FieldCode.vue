<template>
  <div class="field">
    <label :for="`generator-${alias}`">{{ name }}</label>
    <PrismEditor
      class="field__editor"
      :id="`generator-${alias}`"
      :name="alias"
      v-model="cache"
      @input="publish()"
      :highlight="highlighter"
      line-numbers
    />

    <small>
      <slot name="description">{{ description }}</slot>
    </small>
  </div>
</template>

<script>
import { PrismEditor } from 'vue-prism-editor';
import 'vue-prism-editor/dist/prismeditor.min.css';

import { highlight, languages } from 'prismjs/components/prism-core';
import 'prismjs/components/prism-yaml';
import 'prismjs/themes/prism-tomorrow.css';

export default {
  name: 'FieldCode',
  components: {
    PrismEditor,
  },
  data() {
    return {
      cache: null,
    };
  },
  mounted() {
    this.cache = this.value;
  },
  watch: {
    value(value) {
      this.cache = value;
    },
  },
  model: {
    prop: 'value',
    event: 'change'
  },
  props: {
    alias: {
      type: String,
      required: true,
    },
    // The human readable name
    name: {
      type: String,
      required: true,
    },
    value: {
      required: true,
    },
    description: {
      type: String,
      default: '',
    },
    language: {
      type: String,
      required: true,
    },
  },
  methods: {
    highlighter(code) {
      return highlight(code, languages.yaml);
    },
    publish() {
      this.$emit('change', this.cache);
    },
  },
};
</script>

<style lang="css">
.prism-editor__textarea:focus {
  outline: none;
}
</style>

<style lang="css" scoped>
  label {
    display: block;
    margin-bottom: 0.5rem;
    transition: color 0.2s ease-out;
  }
  .field__editor {
    padding: 0.5rem;
    border-radius: 3px;
    border: 1px solid #ddd;
    box-sizing: border-box;
    transition: border-color 0.2s ease-out;
  }
  .field__editor:hover {
    border-color: #ccc;
  }
  .field__editor:focus {
    outline: none;
    border-color: #d69e2e;
  }
  ::placeholder {
    font-style: italic;
    color: #CCC;
  }
  .field__editor + small {
    display: block;
    margin-top: 0.5rem;
    font-style: italic;
    color: #999;
  }
</style>