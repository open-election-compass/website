# Anleitung: Übersetzungen überschreiben

Es ist möglich, die Standard-Übersetzungen zu überschreiben oder sogar eine komplett eigene
Übersetzung hinzuzufügen, z.B. für eine nicht unterstützte Sprache. Dies ist eine fortgeschrittene
Funktion, die etwas mehr technische Fähigkeiten erfordert. Diese Anleitung zeigt dir, wie es geht.

## Einleitung zum Format – Was ist JSON?

JSON ist eine Auszeichnungssprache, die häufig für Konfigurationsdateien und Übersetzungen verwendet
wird. Dies liegt daran, dass es ein einfaches Format ist und zudem sehr kompakt. Es
gibt jedoch einige kleinere Herausforderungen.

Fangen wir einfach mit einem Beispiel an:

```json
{
  "apples": 2,
  "bananas": 5,
  "peaches": 3
}
```

Dies ist eine einfache JSON-Datei mit drei Schlüssel-Wert-Paaren. Beachte, dass das die Anführungszeichen um den Schlüssel zwingend erforderlich sind. Neben Zahlen kann man natürlich auch Zeichenketten
verwenden. Zeichenketten müssen ebenfalls in Anführungszeichen stehen. Achte darauf, doppelte
Anführungszeichen (`"`) zu verwenden anstelle der typografisch korrekten Anführungszeichen (`“”`).

```json
{
  "chocolate": "As much as you want!"
}
```

Außerdem werden Schlüssel-Wert-Paare in geschweifte Klammern gesetzt (`{`). Diese können auch als eigene Objekte oder Level verstanden werden.
Eine JSON-Datei kann stets nur ein Wurzel-Objekt enthalten.

Als nächstes: Verschachtelung. Dies ermöglicht es uns, Optionen oder Zeichenketten zu gruppieren und
unserem Dokument eine Struktur zu geben.

```json
{
  "fruit": {
    "apples": 2,
    "bananas": 5,
    "peaches": 3
  },
  "sweets":  {
    "chocolate": "As much as you want!"
  }
}
```

Jetzt haben wir zwei Gruppen. Die Elemente innerhalb einer Gruppe werden mit genau (!) zwei
Leerzeichen eingerückt. Fügen wir eine weitere Ebene hinzu:

```json
{
  "fruit": {
    "regional": {
      "apples": 2,
      "peaches": 3
    },
    "remote": {
      "bananas": 5
    }
  },
  "sweets": {
    "chocolate": "As much as you want!"
  }
}
```

Beachte, dass das letzte Schlüssel-Wert-Paar in einem Objekt nicht mit einen Komma beendet wird.

Wir können so viele Ebenen hinzufügen, wie wir benötigen. Eine solche Gruppierung ist nicht nur für
die Lesbarkeit und Organisation hilfreich, Programmierer:innen können auch leicht durch diese
Strukturen navigieren, um den gesuchten Wert mithilfe der Punktnotation zu finden. Wenn ich zum
Beispiel wissen wollte, wie viele Pfirsiche ich bekommen soll, könnte ich den "Pfad" so beschreiben:
`fruit.regional.peaches` und bekomme dafür `3` zurück.

JSON kann manchmal etwas knifflig sein. Solltest
du Probleme mit deiner JSON-Formatierung haben, kannst du deine Datei mit einem Dienst wie
<https://jsonlint.com/> validieren.

## Die unterstützten Sprachen

Aktuell werden die folgenden Sprachen offiziell unterstützt:

- Deutsch
- Englisch

Die folgenden Übersetzungen wurden durch die Community bereitgestellt:

- Arabisch (ar)
- Aserbaidschanisch (az)
- Bengali (bn)
- Chinesisch Traditionell (zh-TW)
- Kroatisch (hr)
- Griechisch (el)
- Hausa (ha)
- Hebräisch (he)
- Hindi (hi)
- Ungarisch (hu)
- Indonesisch (id)
- Italienisch (it)
- Japanisch (ja)
- Kinjaruanda (rw)
- Koreanisch (ko)
- Malajalam (ml-IN)
- Persisch (fa)
- Polnisch (pl)
- Portugiesisch, Brazilian (pt-BR)
- Rumänisch (ro)
- Russisch (ru)
- Serbisch, Kyrillisch (sr)
- Slovakisch (sk)
- Spanisch (es-ES)
- Swahili (sw)
- Thailändisch (th)
- Türkisch (tr)
- Ukrainisch (uk)
- Vietnamesisch (vi)

### Wie überschreibt man Übersetzungen?

Jede offiziell unterstützte Sprache hat eine eigene JSON-Datei auf GitHub. Genauer gesagt gibt es
für jede Sprache zwei Dateien:

- [OpenElectionCompass UI](https://github.com/open-election-compass/ui/tree/master/src/locales)
  (eine Sammlung von generischen UI-Komponenten)
- [OpenElectionCompass Client](https://github.com/open-election-compass/client/tree/master/src/locales)
  (die Kernsoftware; das ist höchstwahrscheinlich das, wonach du suchst)

Wenn du eine ganz eigene Sprache verwenden möchtest, benötigst du beide. Wenn du nur einzelne
Zeichenketten überschreiben möchtest, musst du nicht alles kopieren. Schauen wir uns ein Beispiel
an: Der große grüne Button, der unten am Bildschirmrand auftaucht, heißt GuideButton und ist
Anfangs beschriftet mit `Zur Einführung`. Wenn ich das mit `Jetzt starten!` überschreiben wollte, müsste
ich Folgendes tun:

1. Die richtige JSON-Datei für die Sprache, die ich überschreiben möchte finden. Gehe auf die obigen
   Links und werfe einen Blick auf `de.json`.
2. Verwende die Suche, um die Zeichenkette `Zur Einführung` in dieser Datei zu finden.

```json
{
  "elements": {
    # ... hier wurden Zeilen weggelassen, aber 'guide-button' ist ein "Kind" von 'elements', das darf nicht übersehen werden!
    "guide-button": {
      "start": "Los geht's",
      "introduction": "Zur Einführung",
      "first-thesis": "Auf zur ersten These",
      "thesis": "Weiter zur nächsten These",
      "party": "Parteien auswählen",
      "match": "Ergebnis anschauen",
      "compare": "Begründungen der Parteien lesen"
    }
  }
}
```

3. Dies scheint der richtige Ort zu sein. Merke dir nun den Pfad: `elements.guide-button.introduction`. Wir
   wollen den Wert an diesem Pfad überschreiben.
4. Öffne nun im Konfigurations-Editor die deutsche Sprache, indem du auf 'Bearbeiten' klickst und
   schau nach dem Feld 'Übersetzung überschreiben'. Wir werden dort etwas JSON einfügen:

```json
{
  "elements": {
    "guide-button": {
      "introduction": "Jetzt starten!"
    }
  }
}
```

5. Beachte, dass du die Struktur replizieren musst, du aber alle Schlüssel und Werte weglassen
   kannst, die du nicht überschreiben möchtest.

### Einige Zeichenketten enthalten merkwürdige Sonderzeichen

Dir begegnen vielleicht einige seltsame Zeichenketten, wie diese hier:

```json
{
  "elements": {
    "kiosk-mode": {
      "description": "Der Wahlkompass wird in jetzt zurückgesetzt und deine Antworten gelöscht. | Der Wahlkompass wird in {count} Sekunde zurückgesetzt und deine Antworten gelöscht. | Der Wahlkompass wird in {count} Sekunden zurückgesetzt und deine Antworten gelöscht."
    }
  }
}
```

Hier passieren zwei Dinge:

- Die Zeichenfolge wird wiederholt und die Wiederholungen werden durch ein `|` getrennt. Dies sind
  Pluralisierungen. Das System wählt automatisch die richtige Pluralisierung aus. Achte nur darauf,
  die Striche `|` und Leerzeichen beizubehalten.
- Die Zeichenketten enthalten geschweifte Klammern: `{count}`. Wie du dir vielleicht schon denkst,
  sind dies Platzhalter, welche vom System durch etwas anderes ersetzt werden. In diesem Fall eine
  Zahl. Du kannst Platzhalter in einem Satz verschieben, aber nicht löschen, ändern oder übersetzen,
  sonst erkennt das System sie nicht wieder.

Versuchen Sie im Allgemeinen einfach, solche Strukturen intakt zu halten 😉

## Eine eigene Sprache anlegen

Bei Bedarf kannst du mit der beschriebenen Methode alle Zeichenfolgen überschreiben, um eine
vollständig benutzerdefinierte Sprache zu erhalten. Kopiere einfach alle Inhalte aus einer Sprache
und verwende diese als Grundlage für deine eigene Übersetzung.

Wir sind ständig bemüht, die Unterstützung für Sprachen zu erweitern. Wenn du deine Übersetzungen
teilen möchtest, setze dich bitte mit uns in Verbindung!