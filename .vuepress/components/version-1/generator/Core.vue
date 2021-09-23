<template>
  <ValidationObserver v-slot="{ invalid }">
    <fieldset>
      <legend>{{ $t('file') }}</legend>
      <FieldFile
        alias="configuration"
        :name="$t('fields.configuration.name')"
        type="file"
        rules="ext:json"
        v-model="configuration"
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
    <hr />
    <a
      class="button"
      v-if="resultBlobUrl !== null"
      :disabled="invalid"
      :href="resultBlobUrl"
      :download="$t('filename')"
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
import { localize, ValidationObserver } from 'vee-validate';
import de from 'vee-validate/dist/locale/de.json';
import en from 'vee-validate/dist/locale/en.json';
import FieldFile from '../../fields/FieldFile';
import FieldInput from '../../fields/FieldInput';
import FieldSelect from '../../fields/FieldSelect';
import LanguageSwitch from '../../LanguageSwitch';
import template from '!!raw-loader!./template/template.html';
import library from '!!raw-loader!./template/open-election-compass.umd.min.js';
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
      },
      resultBlobUrl: null,
    };
  },
  mounted() {
    localize(this.$lang.substring(0, 2));
    this.$watch('configuration', this.generate);
    this.$watch('store', this.generate, { deep: true });
    this.$i18n.locale = this.$lang;
  },
  computed: {
    languages() {
      if (!this.configuration) {
        return [];
      }
      const configuration = JSON.parse(this.configuration);
      if (configuration && Array.isArray(configuration.languages)) {
        return configuration.languages.map((language) => {
          language.uuid = uuid();
          return language;
        });
      }
      return [];
    },
  },
  methods: {
    toBlobLink(content) {
      const blob = new Blob([ content ], { type: 'text/html' });
      return URL.createObjectURL(blob);
    },
    generate() {
      if (!this.configuration) {
        return this.resultBlobUrl = null;
      }
      const configuration = JSON.parse(this.configuration);
      if (this.languages.length <= 0 || typeof this.languages[0].code !== 'string') {
        return this.resultBlobUrl = null;
      }
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
        faviconPixel,
        faviconVector,
        faviconApple,
        library,
        configuration: this.configuration,
      });
      this.resultBlobUrl = this.toBlobLink(html);
      return;
    },
  },
  i18n: {
    messages: {
      'en-US': {
        file: '1. Upload configuration file',
        options: '2. Options',
        seo: '3. Search engine settings',
        download: 'Download',
        filename: 'election-compass.html',
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
