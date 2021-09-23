# Deployment

Aktuell gibt es mindestens drei Wege einen Wahlkompass aufzusetzen und zu bereitzustellen.

## Option 1: Das einfache, Nur-eine-Datei Setup

Diese Methode ist am einfachsten anzuwenden und eignet sich für Personen ohne tiefere technische
Kenntnisse. Du verwendest die auf dieser Website bereitgestellten Werkzeuge, um eine einzelne
HTML-Datei zu generieren. Diese Datei wird auf deinem Webspace bereitgestellt. Und das war es auch
schon!

### Anforderungen

- **Webspace**: Du benötigst einen Webserver oder einen Webspace bei einem Shared Hosting Anbieter.
  Wenn du derzeit eine Website hast, sollte das kein Problem werden.
- **FTP-Access**: Um die erforderlichen Dateien auf deinen Webspace hochzuladen, benötigst du
  Zugriff auf das Dateisystem des Servers. Wenn du eine:n Webentwickler:in zur Hand hast, kannst du
  ihm/ihr Zugriff auf die Verwaltungsoberfläche deines Webspace-Anbieters gewähren oder nach den
  FTP- oder SFTP-Anmeldeinformationen suchen. Alternativ haben die meisten Anbieter einen
  Dateimanager in ihre Verwaltungsoberfläche eingebettet. Um eine einzelne Datei hochzuladen, reicht
  dies aus.
- **SSL Certificate**: Deine Website sollte über HTTPS zugänglich sein. Dies ist jedoch keine
  strenge Anforderung. Es wird empfohlen und wirkt sich positiv auf die Privatsphäre deiner
  Besucher:innen aus.

### Step-by-step

1. Stelle deine Inhalte zusammen und öffne den
   [Konfigurations-Editor](/configurator/version-1/configuration-editor.html). **Füge deine Inhalte
   entsprechend ein.**
2. Wenn du zufrieden mit deinen Eingaben bist, klicke auf **'Herunterladen' und speicher die
   generierte JSON-Datei** auf deiner Festplatte. Du musst nicht verstehen, wie diese Datei
   funktioniert. Du musst nur wissen, dass sie alle Inhalte enthält, die du in den
   Konfigurations-Editor eingegeben hast. Sorge dafür, dass du diese Datei nicht verlierst – wenn du
   später Änderungen vornehmen musst, kannst du sie wieder im Konfigurations-Editor öffnen.
3. Öffne den [HTML-Generator](/configurator/version-1/html-generator.html), **lade deine JSON-Datei hochund klicke auf 'Generieren'**. Der Download deiner HTML-Datei startet. Diese Datei enthält nun die gesamte Software und deine Inhalte.
4. **Lade die HTML-Datei auf deinen Webspace hoch.** Du kannst sie umbenennen, solange die Dateierweiterung `.html` erhalten bleibt. Du kannst sie beispielsweise in das Stammverzeichnis hochladen und zu "kompass.html" umbenennen. Sie wäre dann unter `example.com/kompass.html` zugänglich. Wenn dies nicht der Fall ist, suche nach einem Verzeichnis mit den Namen `web`, `public` oder `static` und versuche, sie dort abzulegen. Wenn das auch nicht hilft, wende dich an einen Fachmann.

## Option 2: Manuelles, statisches Setup

### Skript einbinden

Place in the head of your HTML document. For privacy, security and legal reasons, you should
consider hosting the library yourself. But for a quick test run, the unpkg CDN is fine.

Im Kopf deines HTML-Dokuments platzieren. Aus Datenschutz-, Sicherheits- und rechtlichen Gründen
solltest du erwägen, das Skript selbst bereitzustellen. Aber für einen schnellen Testlauf ist das
unpkg-CDN in Ordnung.

Hinweis: Der UMD-Build enthält eine Vue.js-Version.

```html
<script src="https://unpkg.com/@open-election-compass/client/dist/lib/open-election-compass.umd.min.js" defer>
```

### Die Inhalte laden (deine Konfiguration)

Nun zum Inhalt. Du musst deine Konfiguration einbinden. Im statischen Setup hast du zwei
Optionen:

#### Variante A: Mit einem script-Element

Du kannst die komplette Konfiguration in einem weiteren script-Tag in deinem HTML unterbringen:

```html
<div id="open-election-compass">
   <open-election-compass load-tag="#oec-content" />
</div>

<script type="application/json" id="oec-content">
   // Platziere deine Konfiguration hier. Dies ist auch das Vorgehen des HTML-Generators.
</script>
```

#### Variante B: Von einer separaten URL laden

Alternativ kannst du den OpenElectionCompass anweisen, die Konfiguration von einer bestimmten URL zu
laden. Dies ist langsamer als Variante A.

```html
<div id="open-election-compass">
   <open-election-compass load-url="./your-configuration.json" />
</div>
```

## Option 3: Manuelles Setup mit Nuxt.js

1. Installiere den Client: `npm i @open-election-compass/client` oder `yarn add @open-election-compass/client`.
2. Lade das Modul in deiner `nuxt.config.js`:
   ```javascript
      {
         modules: [
            '@open-election-compass/client/src/nuxtModule.js',
            // ...
         ]
      }
   ```
3. Verwende die Komponente mit dem `load-object` Property:
   ```html
      <OpenElectionCompass :load-object="yourConfiguration" />
   ```

## HTML Attribute

Das `<open-election-compass>` Element kennt eine Reihe von Attributen:

- `load-tag` – Übergebe einen CSS Selektor um eine Konfiguration von diesem Skript-Tag zu laden.
- `load-url` – Übergebe eine URL um die Konfiguration von dort zu laden.
- `load-object` – Übergebe eine Konfiguration als ein JavaScript-Objekt.
- `kiosk-mode` – Aktiviert den Kiosk Modus. Standard: `false`.
- `friends` – Aktiviert den "Multiplayer" Modus, der geteilte Sitzungen erlaubt. Standard: `false`.
- `friends-host` – Die URL der Peer.js Server Instanz. Standard: `'friends.open-election-compass.com'`.
- `friends-port` – Der Port der Peer.js Server Instanz. Standard: `443`.
- `friends-path` – Der Pfad der Peer.js Server Instanz. Standard: `'/'`.