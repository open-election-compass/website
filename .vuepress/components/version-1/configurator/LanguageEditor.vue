<template>
  <modal :visible="visible" @close="$emit('cancel')">
    <template v-if="language !== null">
      <h2 v-if="language.uuid === null">{{ $t('add-language') }}</h2>
      <h2 v-else-if="language.name.length > 0">{{ $t('edit-language') }} '{{ language.name }}'</h2>
      <h2 v-else>{{ $t('edit-language') }}</h2>
      <ValidationObserver v-slot="{ invalid }">
        <form @submit.prevent="$emit('save')">
          <!-- Name -->
          <FieldInput
            alias="name"
            :name="$t('fields.name.name')"
            type="text"
            rules="required"
            v-model="language.name"
            :placeholder="$t('fields.name.placeholder')"
            :description="$t('fields.name.description')"
          />
          <!-- Code -->
          <Field-Input
            alias="code"
            :name="$t('fields.code.name')"
            type="text"
            :rules="{ required: true, alpha: true, length: 2, regex: /^[a-z]+$/ }"
            v-model="language.code"
            :placeholder="$t('fields.code.placeholder')"
          >
            <template v-slot:description>
              <span v-html="$t('fields.code.description')" />
            </template>
          </Field-Input>
          <button type="submit" :disabled="invalid">
            <Icon name="check" /><span>{{ $t('save') }}</span>
          </button>
        </form>
      </ValidationObserver>
    </template>
  </modal>
</template>

<script>
import { ValidationObserver } from 'vee-validate';
import Modal from '../../Modal.vue';
import FieldInput from '../../fields/FieldInput.vue';

export default {
  name: 'LanguageEditor',
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
    language: {
      type: Object,
      default: null,
    },
  },
  components: {
    Modal,
    FieldInput,
    ValidationObserver,
  },
  mounted() {
    this.$i18n.locale = this.$lang
  },
  i18n: {
    messages: {
      'en-US': {
        'add-language': 'Add language',
        'edit-language': 'Edit language',
        'save': 'Save',
        fields: {
          name: {
            name: 'Name',
            placeholder: 'English',
            description: 'The full name of the language, in the language itself.',
          },
          code: {
            name: 'Code',
            placeholder: 'en',
            description: 'A two letter, lowercase language code as defined by <a href="https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes" target="_blank">ISO 639-1</a>. E.g. \'en\' for English, \'de\' for German, etc.',
          },
        }
      },
      'de-DE': {
        'add-language': 'Sprache hinzufügen',
        'edit-language': 'Sprache bearbeiten',
        'save': 'Speichern',
        fields: {
          name: {
            name: 'Name',
            placeholder: 'Deutsch',
            description: 'Der vollständige Name der Sprache, in der Sprache selbst.',
          },
          code: {
            name: 'Code',
            placeholder: 'en',
            description: 'Ein Sprachcode, bestehend aus zwei Kleinbuchstaben, wie in der Norm <a href="https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes" target="_blank">ISO 639-1</a> beschrieben. Beispiel: \'en\' für Englisch, \'de\' für Deutsch, etc.',
          },
        }
      }
    }
  }
};
</script>
