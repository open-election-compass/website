<template>
  <div>
    <StorageButtons :store="store" @reset="reset" />
    <Languages :store="store" />
    <GeneralInformation :store="store" />
    <Parties :store="store" />
    <Theses :store="store" />
    <Analysis :store="store" />
    <Links :store="store" />
    <hr>
    <DownloadButton :store="store" />
  </div>
</template>

<script>
import { localize } from 'vee-validate';
import { v1 as uuid } from 'uuid';
import de from 'vee-validate/dist/locale/de.json';
import en from 'vee-validate/dist/locale/en.json';
import Languages from './Languages.vue';
import GeneralInformation from './GeneralInformation.vue';
import Parties from './Parties.vue';
import Theses from './Theses.vue';
import Analysis from './Analysis.vue';
import Links from './Links.vue';
import DownloadButton from './DownloadButton.vue';
import StorageButtons from './StorageButtons.vue';

// Install vee-validate error messages
localize({
  de,
  en,
});

export default {
  name: 'Version1',
  data() {
    return {
      store: {
        languages: [{ uuid: uuid(), name: 'English', code: 'en', overwrites: '' }],
        algorithm: 'cityblock/approve-neutral-reject',
        title: {},
        subtitle: {},
        introduction: {
          heading: {},
          text: {},
        },
        parties: [],
        theses: [],
        analysis: {
          endpoint: null,
          survey: {},
          institution: {},
        },
        links: [],
      },
    };
  },
  components: {
    Languages,
    GeneralInformation,
    Parties,
    Theses,
    Analysis,
    Links,
    DownloadButton,
    StorageButtons,
  },
  methods: {
    reset() {
      this.$set(this, 'store', {
        languages: [{ uuid: uuid(), name: 'English', code: 'en', direction: 'ltr' }],
        algorithm: 'cityblock/approve-neutral-reject',
        title: {},
        subtitle: {},
        introduction: {
          heading: {},
          text: {},
        },
        parties: [],
        theses: [],
        analysis: {
          endpoint: null,
          survey: {},
          institution: {},
        },
        links: [],
      });
    },
  },
  mounted() {
    localize(this.$lang.substring(0, 2));
  }
};
</script>
