[![License: CC0-1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](http://creativecommons.org/publicdomain/zero/1.0/) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![npm](https://img.shields.io/npm/v/%40iqbspecs%2Fvariable-info)](https://www.npmjs.com/package/@iqbspecs/variable-info)

This package contains of the data specification for Verona API: Variable Info (to be used as an array).

* JSON Schema (see `variable-info.schema.json`)
* Schema documentation based on [AsyncAPI Generator](https://github.com/asyncapi/generator)
* type definitions for TypeScript (see npm-link above)

## Variable identifier validation

Variable ids and aliases may contain ASCII letters, digits, `_` and `-`. They must contain at least one
character and have no technical maximum length. Values are validated as supplied; they are not trimmed or
renamed.

The package exports `isValidVariableIdentifier(value)` for individual values and
`validateVariableList(variables)` for complete lists. List validation returns machine-readable errors for
empty or malformed identifiers, duplicate ids, duplicate aliases and collisions between public identifiers.
Comparisons are case-insensitive. A variable's public identifier is its alias when present and its id otherwise.
The package also contains `variable-validation-cases.json` as a shared conformance suite for implementations.

Read more:

* [All interface specifications of tba plugins](https://iqb-specifications.github.io/concepts/plugins/) (German only)
* [All specifications of IQB](https://iqb-specifications.github.io/) (German only)
* [Learn about TBA](https://iqb-berlin.github.io/tba-info/) (German only)

## To contributors

If you ever need to update the js code for npm without modifying the spec, please do not change major and minor version numbers. Instead, increase the patch number only to keep consistent to the spec.

# Spezifikation

Diese Datenstruktur wird für den Datenaustausch zwischen dem Schemer und der Unit-Definition benutzt. Da beide TBA-Plugins ihre Spezifikation im Detail nicht veröffentlichen sollen, muss es einen anderen Weg geben, die in der UI-Definition der Unit angelegten Variablen mitzuteilen. 'Variablen' sind Werte-Träger, also mit einer ID identifizierbare Quellen von Antwortdaten. Wie genau diese Antwortdaten entstehen oder wo, das ist uninteressant. Nur dass es sie gibt, ist z. B. für die Planung der Kodierung wichtig.

Die folgenden Parameter werden mitgeliefert:

### `id`, `alias`, `type`, `format`

* Eine Variable braucht eine innerhalb der Unit eindeutige Bezeichnung.
* Ein `alias` ist eine alternative ID, die durch User vergeben werden kann. Dann kann die ID der Variable nicht mehr geändert werden und bleibt konstant über alle Verarbeitungsschritte und Versionen. Der Alias kann aber nach Bedarf geändert werden. Das IQB hat diese Trennung eingeführt, als sich Umbenennungen von Variablen häuften und jede Umbenennung Inkonsistenzen verursachte (Ableitungen schlugen fehl, Seitenzustände wurden falsch ermittelt).
* Mögliche Werte für Typ:
  - `STRING`, `INTEGER`, `NUMBER`, `BOOLEAN`: Entsprechend gängiger simpler Datentypen
  - `ATTACHMENT`: bezeichnet ein erzeugtes Medium, z. B. ein Bild. Im Kern handelt es sich hier um einen String, der aber eine Referenz darstellt und dem Auswertungssystem das Auffinden des zugehörigen Mediums ermöglicht.
  - `NO_VALUE`: Der Editor ist überzeugt, dass diese Variable für die Auswertung keine Rolle spielt. Manchmal ist z. B. die Sichtbarkeit eines Elementes wichtig zu speichern, aber es wird nie ein nützlicher Wert erzeugt.
  - `JSON`: Der Wert ist ein stringifiziertes JSON-Objekt. Dies kann bei komplexen Eingabeelementen erforderlich sein, z. B. Text mit eingebetteten Formeln. Diese Antworten werden dann meist manuell kodiert.
  - `CODED`: Hier wird die Antwort bereits durch den Player codiert. Es kann für die Itementwicklung wesentlich angenehmer sein, die richtige bzw. falsche Antwort bereits im Editor zu definieren und nicht erst im Schemer. Der Player schickt dann die Antworten bereits mit der Information `CODE` und `SCORE` und setzt als Antwortstatus `CODING_COMPLETE`. Die Kodierung wird dann als abgeschlossen angesehen und jede Kodierregel ignoriert.
* Werte für `format`:
  - `TEXT_SELECTION`: Ein String, der Daten für eine Textmarkierung enthält
  - `IMAGE`, `CAPTURE_IMAGE`, `AUDIO`: Spezifiziert den Datentyp `ATTACHMENT`
  - `GGB_FILE`, `GGB_VARIABLE`: Strings mit besonderem Bezug zu GeoGebra
  - `NON_NEGATIVE`: spezifiziert `integer` oder `number`
  - `LATEX`, `MATH_ML`, `MATH_TABLE`: Strings mit besonderem Bezug zu mathematischen Formeln oder Ausdrücken
  - `MATH_TEXT_MIX`: Gemischtes JSON-Objekt mit Text und Formeln
  - `CHEM_ELEMENT_SYMBOLS`, `CHEM_MOLECULE`: Antwortformate, die über Chemie-Widgets erzeugt werden.

### `multiple`, `nullable`

Diese booleschen Werte kennzeichnen, ob die Variable ein Array des beschriebenen Typs und Formats enthält und ob der Wert `null` möglich ist.

### `values`, `valuePositionLabels`, `valuesComplete`

Diese Angaben liefern genauere Informationen über die möglichen Werte der Variablen. Wenn `valuesComplete` true ist, dann handelt es sich sogar um eine vollständige Liste, und die Kodierung kann automatisch erfolgen.

