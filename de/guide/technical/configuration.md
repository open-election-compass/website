# Konfiguration

Der Wahlkompass wird im JSON-Format konfiguriert. Die Konfigurationsdatei enthält alle Thesen,
Antworten, Parteien, Logos, Übersetzungen für beliebig viele Sprachen und weitere Einstellungen.

::: tip Kennst du schon den Konfigurations-Editor?
Du kannst die Konfiguration von Hand schreiben, oder du kannst unseren Konfiguratinos-Editor
ausprobieren. Der Konfigurator generiert JSON Dateien in dem Format das Wahlkompasses. Dies ist
besonders nützlich für Menschen ohne technischen Hintergrund.

[:point_right: Konfigurations-Editor](/configurator/version-1/configuration-editor.html)
:::

## Version

Das Schema für Konfigurationsdateien ist versioniert. Aktuell ist Version 1. Damit die Version einer
Konfigurationsdatei leicht bestimmt werden kann, gibt es ein entsprechendes Attribut:

```json
{
  "version": "1"
}
```

## Algorithmus

Im Laufe der Zeit wurden verschiedene Algorithmen und Antwort-Stile entwickelt. Da der
OpenElectionCompass entwickelt wurde um das Verhalten der populärsten Voting Advice Application
Deutschlands zu imitieren, besteht der Standard-Stil aus drei Buttons und dem sogenannten
Cityblock-Algorithmus.

### Verfügbare Algorithmen und Antwort-Stile

- `cityblock/approve-neutral-reject`:
  - Standard
  - Buttons: 3 – Zustimmen, Neutral, Ablehnen
  - Übereinstimmung/Algorithmus: Cityblock
  - [Demo](https://open-election-compass.github.io/client/index.html)
- `cityblock/approve-partly-reject`:
  - Funktioniert genau so, wie der vorherige, allerdings ist der Neutral-Button mit 'Teilweise' beschriftet.
  - Buttons: 3 – Zustimmen, Teilweise, Ablehnen
  - Übereinstimmung/Algorithmus: Cityblock
  - [Demo](https://open-election-compass.github.io/client/demo-luedenscheid.html)
- `hybrid`:
  - Liefert genauere Ergebnisse, da mehr Antwortmöglichkeiten zur Verfügung stehen und in der Auswertung die Richtung stärker berücksichtigt wird.
  - Buttons: 5 – Starke Zustimmung, Zustimmung, Neutral, Ablehnung, Starke Ablehnung
  - Übereinstimmung/Algorithmus: Hybrid
  - [Demo](https://open-election-compass.github.io/client/demo-hybrid.html)

### Konfigurationsbeispiel

```json
{
  "version": "1",
  "algorithm": "cityblock/approve-neutral-reject"
}
```

## Mehrsprachigkeit

Eine einzelne Konfigurationsdatei enthält sämtliche Sprachen, die der Wahlkompass anbieten soll. In
der Konfiguration wird daher zunächst die Auswahl an Sprachen festgelegt.

```json
{
  // Die folgende Zeile kommt aus dem vorherigen Beispiel! Alle Attribute gehören
  // in dasselbe JSON- Objekt. Wir werden jedoch in den restlichen Beispielen auf
  // diese verzichten, um es kurz zu halten.
  "version": "1",
  "languages": [{
    "name": "Deutsch",
    "code": "de"
  }, {
    "name": "English",
    "code": "en"
  }]
}
```

Als `name` wird der Name der Sprache in der Sprache selbst verwendet.

Als `code` wird ein Sprachcode, bestehend aus zwei Kleinbuchstaben, wie in der Norm
<a href="https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes" target="_blank">ISO 639-1</a>
beschrieben, verwendet. Beispiel: `"en"` für Englisch, `"de"` für Deutsch, etc.

Inhaltliche Elemente, wie Thesen und Antworten, können nun in einem Unterobjekt übersetzt werden.
Wie das geht, siehst du im Beispiel des nächsten Abschnitts. Alle Attribute für die ein Objekt mit
Übersetzungen erwartet wird, sind als 'mehrsprachig' markiert.

### Standard-Übersetzungen überschreiben

Jede beliebige Zeichenfolge der Standard-Übersetzungsdateien kann überschreiben werden. Repliziere
einfach die Struktur der YAML-Übersetzungsdateien und deine Übersetzungen werden eingefügt, wenn die
Anwendung geladen wird.

```json
{
  "version": "1",
  "languages": [{
    "name": "English",
    "code": "en"
  }, {
    "name": "Deutsch",
    "code": "de",
    "overwrites": {
      "elements": { // <-- Dies ist ein Schlüssel auf der ersten Ebene der YAML-Übersetzungsdatei
        "guide-button": {
          "introduction": "Jetzt starten!"
        }
      }
    }
  }]
}
```

Mehr zum Überschreiben von Übersetzungen in der Anleitung:
[Übersetzungen überschreiben](/de/guide/technical/overwriting-translations.html).

## Allgemeine Informationen

Auf der obersten Ebene enthält die Konfiguration allgemeine Informationen wie Titel und Untertitel,
welche im Startbereich des Wahlkompasses angezeigt werden.

```json
{
  // version, languages, algorithm, etc.
  // ...
  "title": {
    "de": "Hogwarts Schülervertretung 1993",
    "en": "Hogwarts Head Boy and Girl Elections 1993"
  },
  "subtitle": {
    "de": "Ein Wahlkompass von Hermine Granger",
    "en": "An election compass by Hermione Granger"
  },
  "introduction": {
    "heading": {
      "de": "Willkommen zum Wahlkompass",
      "en": "Welcome to the Election Compass"
    },
    "text": {
      "de": "Alle 5 teilnehmenden Studierenden haben die 10 Thesen des Wahlkompasses ...",
      "en": "All 5 contending students have answered the 10 theses of the Election Compass ..."
    }
  }
}
```

### title <Badge text="mehrsprachig"/>

Der Titel der Wahl. Wird im Startabschnitt als `h1`-Element angezeigt. Sollte präzise sein und nicht
zu lang.

beispiel: `"Allgemeine Wahl der Vereinten Nationen 2050"`

### subtitle <Badge text="mehrsprachig"/>

Der Untertitel, sichtbar im Startabschnitt ist. Sollte, zum Beispiel, die verantwortliche
Organisation erwähnen.

Beispiel: `"Dieser Wahlkompass wird bereitgestellt von deiner örtlichen Demokratie-Unterstützungsgruppe."`

### introduction.heading <Badge text="mehrsprachig"/>

Unterhalb des Startbereiches folgt eine kurze Einleitung, welche mit einer Überschrift beginnt.
Verwende etwa 3 - 5 Grußworte.

Beispiel: `"Willkommen zum Wahlkompass"`

### introduction.text <Badge text="mehrsprachig"/>

Gib eine kurze Einleitung in deinen Wahlkompass. Erläutere an dieser Stelle nicht die Bedienung,
sondern erkläre deinen Besucher:innen, wer du bist und warum du das tust, wieviele Parteien
teilnehmen, etc. Ungefähr 2 - 5 Sätze.

Beispiel: `"Alle 9 zur Wahl aufgestellten Parteien haben an diesem Wahlkompass ..."`

## Parties (Parteien)

Das `parties` Attribut auf der obersten Ebene enthält die Liste (Array) der Parteien:

```json
{
  // version, languages, title, subtitle, etc.
  // ...
  "parties": [{
    "alias": "spew",
    "name": {
      "de": "Bund für Elfenrechte",
      "en": "Society for the Promotion of Elvish Welfare"
    },
    "short": {
      "de": "B.ELFE.R",
      "en": "SPEW"
    },
    "description": {
      "de": "Die Bund für Elfenrechte wurde 1991 gegründet und hat seither große Anstrengungen unternommen um das Leben derer zu verbessern, die uns dienen.",
      "en": "The Society for the Promotion of Elvish Welfare was founded in 1991 and has since put great efforts into improving the lifes of those in our service."
    },
  }, {
    // another party
  }],
}
```

### parties[].name <Badge text="mehrsprachig"/>

Der vollständige Name der Partei, ohne Abkürzungen.

Beispiel: `"Super Demokratische Partei"`

### parties[].short <Badge text="mehrsprachig"/>

Das Kürzel oder der kurze Name der Partei, wie er für gewöhnlich in Logos und Diagrammen auftaucht.

Example: `"SDP"`

### parties[].alias

Die ID der Partei, für technische Zwecke. Verwende hier einfach die Abkürzung der Partei in
Kleinbuchstaben, ohne Leerzeichen, und stelle sicher, dass sie einzigartig ist (keine zwei Parteien
dürfen dieselbe ID verwenden). Die ID kommt in den Thesen-Objekten zum Einsatz um die Positionen den
entsprechenden Parteien zuzuordnen.

Beispiel: `"sdp"`

### parties[].description <Badge text="mehrsprachig"/>

Eine kurze Beschreibung der Geschichte oder der politischen Ausrichtung der Partei.

Beispiel: `"Die SDP wurde im Jahr 2000 gegründet und ist eine sehr coole Partei. Wählt sie. Sie ist sehr gut."`

<small>(Das ist natürlich ein furchtbares Beispiel :wink:)</small>

### parties[].logo <Badge text="optional"/>

Das Logo der Partei. Diese Zeichenkette wird an das `src`-Attribut des `img`-Elements übergeben. Du
kannst daher eine URL oder eine Base64 kodierten Datei verwenden. Der Einfachheit halber ist dies
auch, was der Konfigurations-Editor tut. Welche Variante du auch wählst, stelle sicher, dass die
Dateien so klein wie möglich sind, kompatibel mit allen Browsern und vektorbasiert, wo immer
möglich. SVG-Dateien liefern die besten Ergebnisse, PNG folgt an zweiter Stelle und JPG an dritter.
Die Grafiken sollten die folgenden Maße haben: 400 x 400 px.

Beispiel für Base64: `"data:image/svg+xml;base64,PHN2ZyBjbGlwLXJ1bGU9ImV2ZW5vZGQi..."`
Beispiel für URL: `"https://example.com/logos/sdp.svg"`

## Theses (Thesen)

Das `theses` Attribut auf der obersten Ebene enthält die Liste (Array) an Thesen:

```json
{
  // version, languages, title, parties, etc.
  // ...
  "theses": [{
    "statement": {
      "de": "Der Sprechende Hut soll sich in Zukunft an die DSGVO halten.",
      "en": "The Sorting Hat should implement a GDPR-compliant privacy policy."
    },
    "positions": {
      "spew": {
        "position": "approve",
        "explanation": {
          "de": "Expecto riddikulus aparecium ferula evanesco lacarnum ...",
          "en": "Expecto riddikulus aparecium ferula evanesco lacarnum ..."
        }
      },
      "snape": {
        "position": "reject",
        "explanation": {
          "de": "Lumos amortentia stupefy immobilus patronum. Funnunculus ...",
          "en": "Lumos amortentia stupefy immobilus patronum. Funnunculus ..."
        }
      },
      /* ... weitere Positionen zu dieser These */
    },
  }, {
    /* ... weitere Thesen */
  }],
```

### theses[].title <Badge text="mehrsprachig"/>

Ein kurzer Name der das Themengebiet der These beschreibt. Zwei bis vier Wörter. Wird direkt
oberhalb der eigentlichen These angezeigt.

Beispiel: `"Politische Bildung"`

### theses[].statement <Badge text="mehrsprachig"/>

Die eigentliche These. Wähle deine Worte mit Bedacht! Eine These muss ausgewogen und präzise sein.
Erkläre, was umgesetzt werden soll, aber gehe nicht ins Detail, wie dies erreicht werden könnte.

Beispiel: `"Die Bundesregierung soll politische Bildung stärker finanziell unterstützen."`

Du kannst Definitionen schwieriger Begriffe hinzufügen, indem du diese in eckige Klammern fast und
eine Erläuterung direkt anschließend in spitzen Klammern.

Beispiel: `"Die Bundesregierung soll [Osmose]<Der gerichtete Fluss von Teilchen durch eine selektiv- oder semipermeable Trennschicht.> regulieren."`

Der Begriff wird in der Darstellung der These mit einer gepunkteten Linie markiert und die
Definition beim Überfahren mit der Maus oder per Tap angezeigt.

### theses[].positions[]

Jede These hat eine Liste (Objekt) an Positionen (`positions`) mit Antworten der Parteien:

```json
{
  // thesis-Objekt
  // ...
  "spew": { // Entspricht der ID / dem `alias` Attribut einer Partei
    "position": "approve",
    "explanation": {
      "de": "Expecto riddikulus aparecium ferula evanesco lacarnum ...",
      "en": "Expecto riddikulus aparecium ferula evanesco lacarnum ..."
    }
  },
  {
    // another position
  }
}
```

#### theses[].positions[].position

Die Position der Partei bzgl. der jeweiligen These.

Mögliche Werte:

- `approve` – Die Partei stimmt dieser These zu.
- `neutral` – Die Partei ist zu dieser These neutral.
- `reject` – Die Partei lehnt diese These ab.
- `skip` – Die Partei hat zu dieser These keine Position zur Verfügung gestellt.

#### theses[].positions[].explanation <Badge text="mehrsprachig"/>

Die Antwort der Partei zu dieser These.

Beispiel: `"Wenn all dies für dich durcheinander erscheint, liegt das daran, dass es so ist! ..."`

## Analyse

Für wissenschaftliche Zwecke unterstützt der OpenElectionCompass das Erheben statistischer Daten
über ein Opt-In-Tool. Wenn die Ergebnisse präsentiert werden, werden Besucher:innen in einem kleinen
Fenster aufgefordert, ihre Ergebnisse - anonym - an die Wissenschaft zu spenden. Mit einem Klick
werden die Daten an ein Backend übertragen.

- Antworten der Besucher:innen, inklusive Faktor (die Wichtig-Einstellung)
- Punktzahl aller Parteien, inklusive Auswahl

Darüber hinaus können Besucher:innen weitere Fragen beantworten, um den Datensatz weiter zu
qualifizieren. Wann immer sie eine Frage beantworte, wird ein aktualisierter Datensatz an das
Backend gesendet. Auf diese Weise müssen Besucher:innen nicht alle Fragen beantworten.

- Alter
- Geschlecht
- Bildungsstand
- Wahlabsicht

Dieser Fragenkatalog kann an deine Anforderungen angepasst werden. Im Moment benötigst du dazu
jedoch eine:n Entwickler:in, da dies noch nicht über die Konfiguration möglich ist. In der
Zwischenzeit kannst du auch einen Link zu einem Folgefragebogen bereitstellen. Nachdem alle oben
genannten Fragen beantwortet wurden, können Besucher:innen diesem Link zu deinem benutzerdefinierten
Fragebogen folgen, um noch mehr Einblicke zu gewähren.

::: warning Ein Wort der Warnung
Diese Funktion soll die Wissenschaft unterstützen. Andere Institutionen,
wie die Medien oder politische Parteien, sollten dies nicht nutzen. Darüber hinaus wurde diese
Funktion so entworfen, dass sie die Privatsphäre der Besucher:innen in den Vordergrund stellt. Die
Analyse ist ausschließlich Opt-In, Besucher:innen beantworten nur so viele Fragen wie sie möchten
und ihre Datensätze sind anonym und lassen sich standardmäßig nicht ihrer Person zuordnen. Wenn du
Namen, E-Mail-Adressen usw. sammeln möchtest, dann tue dies bitte im Folgefragebogen und gib dabei
ausdrücklich zu verstehen, dass du die Namen der Besucher:innen mit deren Antworten verknüpfen
kannst. Bitte verwende diese Funktion mit Respekt und Verantwortung.
:::

```json
  // version, languages, title, parties, theses, etc.
  // ...
  "analysis": {
    "endpoint": "https://example.com/data/luedenscheid",
    "institution": {
      "de": "Universität Münster",
      "en": "University Münster"
    },
    "survey": {
      "de": "http://example.com/survey/de",
      "en": "http://example.com/survey/en"
    }
  },
```

### analysis.endpoint

Dies ist die Adresse an die die Datensätze gesendet werden. Du benötigst einen Server, der die
eingehenden Daten empfängt und in einer Datenbank persistiert. So sieht eine solche Anfrage aus:

```json
POST https://example.com/data/luedenscheid

{
  "signature": "qQu1Pr3cTzpK1HzMox-Iv",
  "data": {
    "answers": [{
      "answer": "approve",
      "factor": 1
    }, {
      "answer": "reject",
      "factor": 1
    }, {
      "answer": "strongly-reject",
      "factor": 1
    }, {
      "answer": "skip",
      "factor": 1
    }, {
      "answer": "strongly-approve",
      "factor": 1
    }, {
      "answer": "approve",
      "factor": 2
    }, {
      "answer": "neutral",
      "factor": 1
    }],
    "parties": [{
      "alias": "spew",
      "selected": true,
      "result": 0.7857142857142857
    }, {
      "alias": "snape",
      "selected": true,
      "result": 0.46428571428571425
    }, {
      "alias": "qlp",
      "selected": false,
      "result": 0.17857142857142855
    }, {
      "alias": "fun",
      "selected": true,
      "result": 0.6785714285714285
    }, {
      "alias": "fff",
      "selected": false,
      "result": 0.3571428571428571
    }],
    "analysis": {
      "age": "27",
      "gender": "male",
      "education": "higher",
      "party": "snape"
    }
  }
}
```

Die Signatur ist eine [nanoid](https://github.com/ai/nanoid) welche mit jedem Seitenaufruf erzeugt
wird. Du kannst sie nutzen um die Besucher:innen serverseitig zu identifizieren und ihre Datensätze
zu aktualisieren.

Die restliche Struktur sollte selbsterklärend sein.

Beispiel: `"https://example.com/data/luedenscheid"`

### analysis.institution <Badge text="mehrsprachig"/>

Enthält den Namen deiner Institution / Universität / Forschungsgruppe.

Beispiel: `"Universität Münster"`

### analysis.survey <Badge text="mehrsprachig"/>

Nutze diese Option um einen Folgefragebogen bereitzustellen für Besucher:innen, die alle übrigen
Fragen beantwortet haben.

Beispiel: `"http://example.com/survey/de"`

## Links

Das optionale `footer-links` Attribut auf der obersten Ebene enthält eine Liste (Array) an Links.
Diese Links werden im Fußbereich der Seite sowie am Ende der Navigation angezeigt. Hier kann deine
Startseite, Datenschutzerklärung, Impressum etc. verlinkt werden.

```json
{
  // version, languages, title, parties, theses, etc.
  // ...
  "footer-links": [
    {
      "text": {
        "de": "Über Hogwarts",
        "en": "About Hogwarts"
      },
      "href": {
        "de": "https://open-election-compass.com/de/",
        "en": "https://open-election-compass.com"
      }
    },
    {
      "text": {
        "de": "Über die Schülervertretung",
        "en": "About the Head Boy and Girl"
      },
      "href": {
        "de": "https://open-election-compass.com/de/",
        "en": "https://open-election-compass.com"
      }
    }
  ]
```

### footer-links[].text <Badge text="mehrsprachig"/>

Die Beschriftung des Links. Vorzugsweise kurz und präzise, 1 – 3 Wörter.

Example: `"Datenschutzerklärung"`

### footer-links[].href <Badge text="mehrsprachig"/>

Der eigentliche Link, wie er in der Addresszeile deines Browsers erscheint. Du kannst
unterschiedliche Links je Sprache verwenden für den Fall, dass die verlinkte Ressource ebenfalls
mehrsprachig ist. Andernfalls verwende einfach für jede Sprache denselben Link.

Example: `"https://example.com"`