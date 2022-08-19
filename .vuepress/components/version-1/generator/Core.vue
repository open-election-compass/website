<template>
  <ValidationObserver v-slot="{ invalid }">
    <fieldset>
      <legend>{{ $t('file') }}</legend>
      <FieldFile
        alias="configuration"
        :name="$t('fields.configuration.name')"
        type="file"
        rules="ext:json"
        v-model="rawConfigurationFile"
        preview="configuration"
        returnType="text"
        :description="$t('fields.configuration.description')"
      />
    </fieldset>
    <fieldset v-if="configuration">
      <legend>{{ $t('options') }}</legend>
      <FieldSelect
        alias="kioskMode"
        :name="$t('fields.kioskMode.name')"
        rules=""
        v-model="store.kioskMode"
        :options="[
          { value: 'off', name: $t('fields.kioskMode.options.off') },
          { value: 'on', name: $t('fields.kioskMode.options.on') },
        ]"
        :description="$t('fields.kioskMode.description')"
      />
      <FieldSelect
        alias="friendsEnabled"
        :name="$t('fields.friendsEnabled.name')"
        rules=""
        v-model="store.friendsEnabled"
        :options="[
          { value: 'off', name: $t('fields.friendsEnabled.options.off') },
          { value: 'on', name: $t('fields.friendsEnabled.options.on') },
        ]"
        :description="$t('fields.friendsEnabled.description')"
      />
      <FieldInput
        alias="friendsHost"
        :name="$t('fields.friendsHost.name')"
        type="text"
        rules=""
        v-model="store.friendsHost"
        :placeholder="$t('fields.friendsHost.placeholder')"
        :description="$t('fields.friendsHost.description')"
      />
      <FieldInput
        alias="friendsPort"
        :name="$t('fields.friendsPort.name')"
        type="text"
        rules="numeric"
        v-model="store.friendsPort"
        :placeholder="$t('fields.friendsPort.placeholder')"
      />
      <FieldInput
        alias="friendsPath"
        :name="$t('fields.friendsPath.name')"
        type="text"
        rules=""
        v-model="store.friendsPath"
        :placeholder="$t('fields.friendsPath.placeholder')"
      />
    </fieldset>
    <fieldset v-if="configuration">
      <legend>{{ $t('seo') }}</legend>
      <LanguageSwitch :languages="languages" :values="store.title" v-slot="{selected}">
        <FieldInput
          alias="title"
          :name="$t('fields.title.name')"
          type="text"
          rules="required|min:10|max:200"
          v-model="store.title[selected]"
          :placeholder="$t('fields.title.placeholder')"
          :description="$t('fields.title.description')"
        />
      </LanguageSwitch>
      <LanguageSwitch :languages="languages" :values="store.description" v-slot="{selected}">
        <FieldInput
          alias="description"
          :name="$t('fields.description.name')"
          type="text"
          rules="required|min:10|max:200"
          v-model="store.description[selected]"
          :placeholder="$t('fields.description.placeholder')"
          :description="$t('fields.description.description')"
        />
      </LanguageSwitch>
      <FieldInput
        alias="openGraphImage"
        :name="$t('fields.openGraphImage.name')"
        type="text"
        rules=""
        v-model="store.openGraphImage"
        :placeholder="$t('fields.openGraphImage.placeholder')"
        :description="$t('fields.openGraphImage.description')"
      />
      <FieldInput
        alias="openGraphUrl"
        :name="$t('fields.openGraphUrl.name')"
        type="text"
        rules=""
        v-model="store.openGraphUrl"
        :placeholder="$t('fields.openGraphUrl.placeholder')"
        :description="$t('fields.openGraphUrl.description')"
      />
    </fieldset>
    <fieldset v-if="configuration">
      <legend>{{ $t('languages') }}</legend>
      <FieldSelect
        alias="extractLanguages"
        :name="$t('fields.extract-languages.name')"
        rules=""
        v-model="store.extractLanguages"
        :options="[
          { value: 'off', name: $t('fields.extract-languages.options.no') },
          { value: 'on', name: $t('fields.extract-languages.options.yes') },
        ]"
        :description="$t('fields.extract-languages.description')"
      />

      <FieldSelect
        v-if="store.extractLanguages === 'on'"
        alias="mainLanguage"
        :name="$t('fields.main-language.name')"
        rules=""
        v-model="store.mainLanguage"
        :options="languages.map(language => ({ value: language.uuid, name: language.name }))"
        :description="$t('fields.main-language.description')"
      />
    </fieldset>
    <hr />
    <a
      class="button"
      v-if="resultBlobUrl !== null"
      :disabled="invalid"
      :href="resultBlobUrl"
      :download="store.extractLanguages === 'on' ? $t('filename.zip') : $t('filename.html')"
    >
      <Icon name="download" />
      <span>{{ $t('download') }}</span>
    </a>
  </ValidationObserver>
</template>

<script>
import * as Eta from 'eta';
import tag from 'html-tag';
import { v1 as uuid } from 'uuid';
import cloneDeep from 'lodash/cloneDeep';
import get from 'lodash/get';
import set from 'lodash/set';
import unset from 'lodash/unset';
import { strToU8, zipSync } from 'fflate';
import { localize, ValidationObserver } from 'vee-validate';
import de from 'vee-validate/dist/locale/de.json';
import en from 'vee-validate/dist/locale/en.json';
import FieldFile from '../../fields/FieldFile';
import FieldInput from '../../fields/FieldInput';
import FieldSelect from '../../fields/FieldSelect';
import LanguageSwitch from '../../LanguageSwitch';
import template from '!!raw-loader!./template/template.html';
import library from '!!raw-loader!@open-election-compass/client/dist/open-election-compass.umd.min.js';
import style from '!!raw-loader!@open-election-compass/client/dist/style.css';
import faviconPixel from '!!url-loader!./template/favicon.png';
import faviconVector from '!!url-loader!./template/favicon.svg';
import faviconApple from '!!url-loader!./template/apple-touch-icon.png';

// Install vee-validate error messages
localize({
  de,
  en,
});

export default {
  name: 'Generator',
  components: {
    FieldFile,
    FieldInput,
    FieldSelect,
    LanguageSwitch,
    ValidationObserver,
  },
  data() {
    return {
      rawConfigurationFile: null,
      configuration: null,
      store: {
        description: {},
        title: {},
        kioskMode: 'off',
        friendsEnabled: 'on',
        friendsHost: 'friends.open-election-compass.com',
        friendsPort: 443,
        friendsPath: null,
        openGraphImage: null,
        openGraphUrl: null,
        extractLanguages: 'off',
        mainLanguage: null,
      },
      resultBlobUrl: null,
    };
  },
  mounted() {
    localize(this.$lang.substring(0, 2));
    this.$watch('rawConfigurationFile', this.parseRawConfigurationFile);
    this.$watch('configuration', this.generate);
    this.$watch('store', this.generate, { deep: true });
    this.$watch('store.extractLanguages', this.setDefaultMainLanguage);
    this.$i18n.locale = this.$lang;
  },
  computed: {
    languages() {
      if (!this.configuration) {
        return [];
      }
      if (this.configuration && Array.isArray(this.configuration.languages)) {
        return this.configuration.languages.map((language) => {
          language.uuid = uuid();
          return language;
        });
      }
      return [];
    },
    multilingualConfigurationPaths() {
      if (this.configuration === null) {
        return [];
      }
      const paths = [
        'title',
        'subtitle',
        'introduction.heading',
        'introduction.text',
        'analysis.survey',
        'analysis.institution'
      ]
      for (let p = 0; p < this.configuration.parties.length; p++) {
        paths.push(`parties.${p}.name`);
        paths.push(`parties.${p}.short`);
        paths.push(`parties.${p}.description`);
      }
      for (let t = 0; t < this.configuration.theses.length; t++) {
        paths.push(`theses.${t}.title`);
        paths.push(`theses.${t}.statement`);
        this.configuration.parties.forEach((party) => {
          paths.push(`theses.${t}.positions.${party.alias}.explanation`);
        })
      }
      return paths;
    }
  },
  methods: {
    setDefaultMainLanguage() {
      this.store.mainLanguage = this.configuration.languages[0].uuid;
    },
    async getUiLocales(code) {
      return (await import(`@open-election-compass/ui/src/locales/${code}.json`)).default.ui
    },
    async getClientLocales(code) {
      return (await import(`@open-election-compass/client/src/locales/${code}.json`)).default
    },
    /**
     * Extracts the given language from configuration, mutating the configuration in the process.
     */
    extractLanguageFromConfiguration(configuration, code) {
      const extraction = {};
      const paths = this.multilingualConfigurationPaths;
      paths.forEach((unsuffixed) => {
        const path = `${unsuffixed}.${code}`;
        set(extraction, unsuffixed, get(configuration, path));
        unset(configuration, path);
      });
      return extraction;
    },
    parseRawConfigurationFile() {
      if (typeof this.rawConfigurationFile !== 'string') {
        this.configuration = null;
        return;
      }
      this.configuration = JSON.parse(this.rawConfigurationFile);
    },
    async generate() {
      if (!this.configuration) {
        return this.resultBlobUrl = null;
      }
      console.log('Generating...')
      const configuration = cloneDeep(this.configuration);
      if (this.languages.length <= 0 || typeof this.languages[0].code !== 'string') {
        return this.resultBlobUrl = null;
      }

      // Compile locales, with language extraction if needed
      const locales = []
      for (const language of this.languages) {
        const locale = {
          ui: (await this.getUiLocales(language.code)),
          client: (await this.getClientLocales(language.code)),
        }
        if (this.store.extractLanguages === 'on' && language.uuid !== this.store.mainLanguage) {
          locale.content = this.extractLanguageFromConfiguration(configuration, language.code);
        }

        // Add hint on where to find locales to the configuration
        const languageConfiguration = configuration.languages.find(item => item.code === language.code);
        if (this.store.extractLanguages === 'on' && language.uuid !== this.store.mainLanguage) {
          languageConfiguration['load-from-url'] = `./oec-locale-${language.code}.json`
        } else {
          languageConfiguration['load-from-tag'] = `#oec-locales-${language.code}`
        }
        locales.push({
          code: language.code,
          uuid: language.uuid,
          json: JSON.stringify(locale),
        })
      }

      const mainLocale = locales.find(locale => locale.uuid === this.store.mainLanguage);

      const html = Eta.render(template, {
        tag,
        languages: this.languages,
        defaultLanguage: this.languages[0],
        kioskMode: this.store.kioskMode,
        friendsEnabled: this.store.friendsEnabled,
        friendsHost: this.store.friendsHost,
        friendsPort: this.store.friendsPort,
        friendsPath: this.store.friendsPath,
        title: this.store.title,
        description: this.store.description,
        openGraphImage: this.store.openGraphImage,
        openGraphUrl: this.store.openGraphUrl,
        extractLanguages: this.store.extractLanguages,
        mainLanguage: this.store.mainLanguage,
        locales,
        mainLocale,
        faviconPixel,
        faviconVector,
        faviconApple,
        library,
        style,
        configuration: JSON.stringify(configuration),
      });

      // Generate ZIP if language are extracted and we have multiple output files
      if (this.store.extractLanguages === 'on') {
        const files = {
          'index.html': strToU8(html),
        }
        locales.forEach((locale) => {
          if (locale.uuid === this.store.mainLanguage) {
            return; // main language is not extracted
          }
          files[`oec-locale-${locale.code}.json`] = strToU8(locale.json);
        });
        const zipped = zipSync(files);
        this.resultBlobUrl =  URL.createObjectURL(new Blob([zipped], { type: 'application/zip' }));
      } else {
        const blob = new Blob([ html ], { type: 'text/html' });
        this.resultBlobUrl =  URL.createObjectURL(blob);
      }
    },
  },
  i18n: {
    messages: {
      'en-US': {
        file: '1. Upload configuration file',
        options: '2. Options',
        seo: '3. Search engine settings',
        languages: '4. Languages',
        download: 'Download',
        filename: {
          html: 'election-compass.html',
          zip: 'election-compass.zip'
        },
        fields: {
          title: {
            name: 'Title',
            description: 'The title will be displayed in the browser tab. Use something descriptive and to the point. 50 - 60 characters are perfect for search results.',
            placeholder: 'Election Compass, Hogwarts 1993'
          },
          description: {
            name: 'Description',
            description: 'The description is displayed as the excerpt in search results and in the preview of many social media platforms. Use it to advertise your service. 50 - 160 characters work best.',
            placeholder: 'The parties have declared their position – now it is your turn to vote on the most important political decisions for the years to come.'
          },
          kioskMode: {
            name: 'Kiosk Mode',
            description: 'Activate the Kiosk Mode if you want to display your election compass in a public place. The system will show an option to restart the election compass at any time and will do so automatically after a certain time of inactivity.',
            options: {
              off: 'disabled',
              on: 'enabled',
            },
          },
          friendsEnabled: {
            name: 'Shared Sessions aka Friends (Multiplayer Mode)',
            description: 'Activate Shared Sessions to allow users to connect to friends using a secure and direct peer-to-peer connection.',
            options: {
              off: 'disabled',
              on: 'enabled',
            },
          },
          friendsHost: {
            name: 'Server for Shared Sessions, Hostname',
            description: 'For browsers to find each other and establish a peer-to-peer connection for Shared Sessions, they need a signalling server. The actual data is transferred directly from browser to browser to avoid privacy issues. The OpenElectionCompass project maintains such a server, but you can also host your own using Peer.js.',
            placeholder: 'friends.open-election-compass.com'
          },
          friendsPort: {
            name: 'Server for Shared Sessions, Port',
            placeholder: '9000'
          },
          friendsPath: {
            name: 'Server for Shared Sessions, Path',
            placeholder: '/'
          },
          openGraphImage: {
            name: 'Open Graph Image',
            description: 'Many social networks support a standard called Open Graph. This allows you to provide content that is used to provide a preview whenever someone shares a link to your election compass on Facebook, Twitter, etc. The preview typically consists of a title, description and optionally an image. If you have a suiting image, make it publicly available on a webserver and enter the URL here. Use a 1200 x 630 px graphic, preferably PNG.',
            placeholder: 'https://example.com/election-compass/social-media-image.png'
          },
          openGraphUrl: {
            name: 'Open Graph Url',
            description: 'To make sure, the correct URL is used on various social media platforms, you can set the URL where your election compass will be available.',
            placeholder: 'https://example.com/election-compass'
          },
          'extract-languages': {
            name: 'Extract translations',
            options: {
              yes: 'Yes',
              no: 'No',
            },
            description: 'By default, all translations are embedded into the HTML file. If you\'re using many languages it is recommended to extract all languages but the default one. This will generate additional files that will be loaded on demand.'
          },
          'main-language': {
            name: 'Default language',
            description: 'The translations of the default language will still be embedded, all other will be extracted.'
          },
          configuration: {
            name: 'Configuration file',
            description: 'A configuration file in the JSON file format. You can write one from scratch, or you can use the Configuration Editor.',
          },
        },
      },
      'de-DE': {
        file: '1. Konfigurationsdatei hochladen',
        options: '2. Suchmaschinen-Einstellungen',
        seo: '3. Suchmaschinen-Einstellungen',
        languages: '4. Sprachen',
        download: 'Herunterladen',
        filename: 'wahlkompass.html',
        fields: {
          title: {
            name: 'Titel',
            description: 'Der Titel wird im Browser-Tab angezeigt. Verwende etwas aussagekräftiges und präzises. 50 - 60 Zeichen sind perfekt für Suchergebnisse.',
            placeholder: 'Wahlkompass, Hogwarts 1993'
          },
          description: {
            name: 'Beschreibung',
            description: 'Die Beschreibung wird als Auszug in den Suchergebnissen und als Vorschau in sozialen Netzwerken angezeigt. Verwende sie um dein Angebot zu bewerben. 50 - 160 Zeichen funktionieren am besten.',
            placeholder: 'Die Parteien haben ihre Positionen bestimmt – nun bist du an der Reihe! Stimme ab über die wichtigsten politischen Entscheidungen der kommenden Jahre.'
          },
          kioskMode: {
            name: 'Kiosk Modus',
            description: 'Aktiviere den Kiosk Modus, falls du deinen Wahlkompass an einem öffentlichen Ort ausstellen möchtest. Das System wird eine Möglichkeit zum Zurücksetzen des Wahlkompasses anbieten und diese nach einer bestimmten Inaktivitätsperiode auch automatisch ausführen.',
            options: {
              off: 'deaktiviert',
              on: 'aktiviert',
            },
          },
          friendsEnabled: {
            name: 'Gemeinsame Sitzungen aka Freunde (Multiplayer Modus)',
            description: 'Aktiviere Gemeinsame Sitzungen und ermögliche Besucher:innen eine sichere und direkte Peer-to-Peer Verbindung mit Freund:innen herzustellen.',
            options: {
              off: 'deaktiviert',
              on: 'activiert',
            },
          },
          friendsHost: {
            name: 'Server für Gemeinsame Sitzungen, Hostname',
            description: 'Damit Browser sich gegenseitig finden und eine Peer-to-Peer Verbindung für eine Gemeinsame Sitzung aufbauen können, benötigen sie einen Signalling Server. Die eigentlichen Daten werden direkt von Browser an Browser übertragen um Datenschutzprobleme zu vermeiden. Das OpenElectionCompass Projekt betreibt einen solchen Server, du kannst jedoch mit Peer.js auch deinen eigenen aufsetzen.',
            placeholder: 'friends.open-election-compass.com'
          },
          friendsPort: {
            name: 'Server für Gemeinsame Sitzungen, Port',
            placeholder: '9000'
          },
          friendsPath: {
            name: 'Server für Gemeinsame Sitzungen, Path',
            placeholder: '/'
          },
          openGraphImage: {
            name: 'Open Graph Grafik',
            description: 'Viele soziale Netzwerke unterstützen einen Standard namens Open Graph. Dies ermöglicht es dir, Inhalte bereitzustellen, welche für eine Vorschau deines Wahlkompasses verwendet werden, wann immer jemand einen Link zu deinem Wahlkompass auf Facebook, Twitter und Co. teilt. Die Vorschau besteht aus einem Titel, einer Beschreibung und einem optionalen Bild. Wenn du ein geeignetes Bild zur Hand hast, mache es auf einem Webserver öffentlich zugänglich und füge die URL hier ein. Verwende eine 1200 x 630 px Grafik, vorzugsweise PNG.',
            placeholder: 'https://example.com/election-compass/social-media-image.png'
          },
          openGraphUrl: {
            name: 'Open Graph Url',
            description: 'Um sicher zu gehen, dass die korrekte URL in diversen sozialen Netzwerken verwendet wird, kannst du hier die URL setzen, unter der dein Wahlkompass zur Verfügung stehen wird.',
            placeholder: 'https://example.com/election-compass'
          },
          'extract-languages': {
            name: 'Sprachen auslagern',
            options: {
              yes: 'Ja',
              no: 'Nein',
            },
            description: 'Standardmäßig werden alle Übersetzungen in die HTML-Datei eingebettet. Wenn du viele Sprachen verwendest, empfiehlt es sich, alle Sprachen außer der Standard-Sprache auszulagern. Dadurch werden zusätzliche Dateien generiert, welche auf Bedarf nachgeladen werden.'
          },
          'main-language': {
            name: 'Standard-Sprache',
            description: 'Die Übersetzungen der Standard-Sprache werden weiterhin eingebettet, alle übrigen werden ausgelagert.'
          },
          configuration: {
            name: 'Konfigurationsdatei',
            description: 'Eine Konfigurationddatei im JSON Dateiformat. Du kannst sie von Hand schreiben, oder den Konfigurations Editor verwenden.',
          },
        }
      }
    }
  }
};
</script>
