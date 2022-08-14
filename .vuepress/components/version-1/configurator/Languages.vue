<template>
  <div>
    <language-editor
      :visible="language !== null"
      :language="language"
      @save="save"
      @cancel="cancel"
    />
    <fieldset>
      <legend>{{ $t('title') }}</legend>
      <p>{{ $t('explanation') }}</p>
      <SlickList
        lockAxis="y"
        :useDragHandle="true"
        v-model="store.languages"
        class="items"
      >
        <SlickItem
          v-for="(language, index) in store.languages"
          :index="index"
          :key="language.uuid"
          class="item"
        >
          <span v-handle class="handle"><Icon name="bars" /></span>
          <span>
            <strong>{{ language.name }} ({{ language.code }})</strong>
          </span>
          <button @click="edit(language)" class="small">
            <Icon name="edit" /><span>{{ $t('edit') }}</span>
          </button>
          <button @click="remove(language)" class="small" :disabled="store.languages.length < 2">
            <Icon name="trash" /><span>{{ $t('remove') }}</span>
          </button>
        </SlickItem>
      </SlickList>
      <button @click="add" class="add small">
        <Icon name="plus" /><span>{{ $t('add') }}</span></button>
    </fieldset>
  </div>
</template>

<script>
import { v1 as uuid } from 'uuid';
import _filter from 'lodash/filter';
import { SlickList, SlickItem, HandleDirective } from 'vue-slicksort';
import LanguageEditor from './LanguageEditor.vue';

export default {
  name: 'Languages',
  data() {
    return {
      language: null, // which language should be edited, if any
    };
  },
  props: {
    store: {
      type: Object,
      required: true,
    },
  },
  mounted() {
    this.$i18n.locale = this.$lang
  },
  methods: {
    add() {
      this.$set(this, 'language', {
        uuid: null, // indicates, that this language is not yet present in the languages array
        name: '',
        code: '',
        direction: 'ltr',
        overwrites: '',
      });
    },
    edit(language) {
      this.$set(this, 'language', JSON.parse(JSON.stringify(language))); // clone language
    },
    findIndexOfLanguage(language) {
      return this.store.languages.findIndex(item => item.uuid === language.uuid);
    },
    save() {
      if (this.language === null) {
        return;
      }

      // Add new language to languages array
      if (this.language.uuid === null) {
        this.language.uuid = uuid();
        this.store.languages.push(this.language);
        this.language = null;
        return;
      }

      // Save existing language
      const originalLanguage = this.store.languages[this.findIndexOfLanguage(this.language)];
      Object.assign(originalLanguage, this.language);
      this.language = null;
      return;
    },
    cancel() {
      this.language = null;
    },
    remove(language) {
      const index = this.findIndexOfLanguage(language);
      this.store.languages.splice(index, 1);
    },
  },
  components: {
    SlickList,
    SlickItem,
    LanguageEditor,
  },
  directives: {
    handle: HandleDirective,
  },
  i18n: {
    messages: {
      'en-US': {
        title: '1. Languages',
        explanation: 'The first language will also be the default language if the browsers language doesn\'t match any other language.',
        edit: 'Edit',
        remove: 'Remove',
        add: 'Add language'
      },
      'de-DE': {
        title: '1. Sprachen',
        explanation: 'Die oberste Sprache ist gleichzeitig die Standardsprache, falls die im Browser eingestellte Sprache zu keiner unterstützten Sprache passt.',
        edit: 'Bearbeiten',
        remove: 'Löschen',
        add: 'Sprache hinzufügen'
      }
    }
  }
};
</script>

<style lang="css" scoped>
  .items {
    list-style-type: none;
    padding-left: 0;
  }
  .item {
    padding: 1rem;
    border: 1px solid #DDD;
    background-color: #F2F2F2;
    border-radius: 3px;
    display: flex;
    align-items: center;
    margin-top: 0.5rem;
    user-select: none;
  }
  .item > .handle {
    flex: 0 1 auto;
    margin-right: 0.75rem;
    cursor: grab;
  }
  .item > span {
    flex: 1 1 0%;
    justify-self: flex-start;
    margin-right: 0.5rem;
  }
  .item > button {
    flex: 0 1 auto;
    justify-self: flex-end;
  }
  .item > button + button {
    margin-left: 0.5rem;
  }
  .add {
    margin-top: 1rem;
  }
</style>