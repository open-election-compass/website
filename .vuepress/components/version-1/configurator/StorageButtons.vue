<template>
  <div class="buttons">
    <button class="small" @click="loadFromBrowser">
      <Icon name="history" />
      <span>{{ $t('load-from-localstorage') }}</span>
    </button>
    <button class="small" @click="openFileDialog">
      <Icon name="upload" />
      <span>{{ $t('load-from-file') }}</span>
    </button>
    <input type="file" class="file-button" @input="loadFromFile($event)" />
    <button class="small" @click="$emit('reset')">
      <Icon name="trash" />
      <span>{{ $t('clear-localstorage') }}</span>
    </button>
  </div>
</template>

<script>
import { v1 as uuid } from 'uuid';
import _forEach from 'lodash/forEach';
import yaml from 'js-yaml';

export default {
  name: 'StorageButtons',
  props: {
    store: {
      type: Object,
      required: true,
    },
  },
  methods: {
    loadFromBrowser() {
      const existing = localStorage.getItem('oec-v1-config');
      if (existing) {
        this.load(existing);
      } else {
        alert('The browser storage appears to be empty :(');
      }
    },
    openFileDialog() {
      this.$el.querySelector('input.file-button').click();
    },
    loadFromFile(event) {
      if (event.target.files === undefined || event.target.files[0] === undefined) {
        return;
      }
      const file = event.target.files[0];
      if (file.type !== 'application/json') {
        alert('Please upload a JSON file.')
        return;
      }
      const reader = new FileReader();
      reader.onload = () => {
        this.load(reader.result);
      };
      reader.readAsText(file);
    },
    /*
     * Translations are keyed by language.uuid internally. The format however provides translations
     * keyed by the language code (e.g. 'en'), because this is shorter and more human-readable. This
     * method converts the language code keys to uuid keys.
     */
    keyTranslationsByLanguageUuid(languages, translations) {
      const converted = {};
      _forEach(languages, ({code, uuid}) => {
        converted[uuid] = translations[code];
      });
      return converted;
    },
    load(json) {
      this.$emit('reset');

      this.$nextTick(() => {
        const configuration = JSON.parse(json);

        this.store.version = configuration.version;
        this.store.algorithm = configuration.algorithm;

        configuration.languages.forEach((language) => {
          this.store.languages.push({
            uuid: uuid(),
            name: language.name,
            code: language.code,
            direction: language.direction,
            overwrites: typeof language.overwrites === 'object' ? yaml.dump(language.overwrites) : '',
          });
        });

        // Remove first language as this was the default language added when resetting the store:
        this.store.languages.splice(0, 1);

        this.store.title = this.keyTranslationsByLanguageUuid(this.store.languages, configuration.title);
        this.store.subtitle = this.keyTranslationsByLanguageUuid(this.store.languages, configuration.subtitle);
        this.store.introduction.heading = this.keyTranslationsByLanguageUuid(this.store.languages, configuration.introduction.heading);
        this.store.introduction.text = this.keyTranslationsByLanguageUuid(this.store.languages, configuration.introduction.text);
        configuration.parties.forEach((party) => {
          this.store.parties.push({
            uuid: uuid(),
            alias: party.alias,
            name: this.keyTranslationsByLanguageUuid(this.store.languages, party.name),
            short: this.keyTranslationsByLanguageUuid(this.store.languages, party.short),
            description: this.keyTranslationsByLanguageUuid(this.store.languages, party.description),
            logo: party.logo,
          });
        });
        configuration.theses.forEach((thesis) => {
          const positions = {};
          this.store.parties.forEach(party => {
            if (thesis.positions[party.alias]) {
              positions[party.uuid] = {
                position: thesis.positions[party.alias].position,
                explanation: this.keyTranslationsByLanguageUuid(this.store.languages, thesis.positions[party.alias].explanation),
              };
            }
          });
          this.store.theses.push({
            uuid: uuid(),
            title: this.keyTranslationsByLanguageUuid(this.store.languages, thesis.title),
            statement: this.keyTranslationsByLanguageUuid(this.store.languages, thesis.statement),
            positions,
          });
        });
        if (configuration.analysis) {
          this.store.analysis.endpoint = configuration.analysis.endpoint;
          this.store.analysis.institution = this.keyTranslationsByLanguageUuid(this.store.languages, configuration.analysis.institution);
          this.store.analysis.survey = this.keyTranslationsByLanguageUuid(this.store.languages, configuration.analysis.survey);
        }
        if (Array.isArray(configuration['footer-links'])) {
          configuration['footer-links'].forEach((link) => {
            this.store.links.push({
              uuid: uuid(),
              text: this.keyTranslationsByLanguageUuid(this.store.languages, link.text),
              href: this.keyTranslationsByLanguageUuid(this.store.languages, link.href),
            });
          });
        }
      });
    },
  },
  mounted() {
    this.$i18n.locale = this.$lang
  },
  i18n: {
    messages: {
      'en-US': {
        'load-from-localstorage': 'Load from browser storage',
        'load-from-file': 'Load from file',
        'clear-localstorage': 'Clear browser storage'
      },
      'de-DE': {
        'load-from-localstorage': 'Aus Browserspeicher laden',
        'load-from-file': 'Aus Datei laden',
        'clear-localstorage': 'Browserspeicher leeren'
      }
    }
  }
};
</script>

<style scoped>
.buttons {
  padding-top: 1rem;
}
.file-button {
  position: fixed;
  top: -100vh;
}
</style>