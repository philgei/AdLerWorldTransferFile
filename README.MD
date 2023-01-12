# Das AdLer World Transfer File (AWT)

Das Dateiformat dient zum Austausch von Daten zwischen den 3 AdLer-Komponenten AMG, Backend und 3D-Frontend.

## Datenstruktur

Die Struktur der gesamten Lernwelt sowie übergeordneten Metadaten wird über ein einzigen JSON-Objekt abgebildet.
Zum jetzigen Zeitpunkt befindet sich auf dem Root-Level dieses Objekts rein die Versionsnummer des Files sowie ein Objekt, welches die Lernwelt mit all ihren
Inhalten beschreibt.

## Versionierung

Um sicherzustellen, dass die Komponenten ordnungsgemäß kommunizieren können, wird das AWT-File mit einer Versionsnummer versehen. Diese Versionsnummer wird
auf dem Root-Level des JSON-Objekts gespeichert. Die Komponenten können nun überprüfen, ob sie mit der Version des Files umgehen können.

## Vorgeschichte

Das AWT File wurde im laufe der Entwicklung des AdLer-System stetig weiterentwickelt. Allerdings wurden diese Entwicklungen nicht, oder nicht ausreichend Dokumentiert. Deshalb startet das AFT-Format mit der Versionsnummer 0.3. Alle vorherigen Änderungen sind nicht einzusehen und nicht im Changelog vorhanden.

## Konventionen

Die Konventionen, die in diesem Dokument verwendet werden, richten sich grundlegend nach der [JSON-Spec](https://www.json.org/json-de.html).

### Normalisierung der Daten

Zusätzlich dazu wird auf eine Normalisierung der Datenstruktur geachtet. Das bedeutet, dass die Datenstruktur immer gleich ist, unabhängig von der Anzahl der
Elemente. Das führt dazu, Elemente, welche öfters in verschiedenen Objekten
vorkommen, in eigene Objekte ausgelagert werden und per Id Referenziert werden.

### Singular vs Plural

Die Namen von schlüsseln, welche einen einzigen Wert beinhalten, sollen immer im Singular geschrieben werden.
Die Namen von Schlüsseln, welche mehrere Werte (Arrays) beinhalten, sollen immer im Plural geschrieben werden.

## Beschreibung der einzelnen Felder

### Beschreibung der Felder der Welt

| Schlüssel            | Datentyp | Funktion                                                                                     | benötigt? |
| -------------------- | -------- | -------------------------------------------------------------------------------------------- | --------- |
| lmsElementIdentifier | objekt   | Objekt, welches durch Angabe von Typ und Wert ein Elementen im LMS eindeutig bezeichnen kann | Ja        |
| worldDescription     | string   | Beschreibung der Lernwelt, welche dem Nutzer angezeigt wird                                  | Nein      |
| worldGoals           | [string] | Einzelne Lernziele der Welt                                                                  | Nein      |
| worldName            | string   | Name der Welt                                                                                | Ja        |
| spaces            | [object]   | Array aus Spaces, welche in der Welt vorhanden sind                                                                                | Ja        |

### Beschreibung der Felder der Spaces

| Schlüssel                | Datentyp                  | Funktion                                                                                                       | benötigt? |
| ------------------------ | ------------------------- | -------------------------------------------------------------------------------------------------------------- | --------- |
| lmsElementIdentifier     | objekt                    | Objekt, welches durch Angabe von Typ und Wert ein Elementen im LMS eindeutig bezeichnen kann                   | Ja        |
| spaceContents            | [int]                     | ids der LernElemente, welche sich in dem Raum befinden                                                         | Ja        |
| includedPoints           | int                       | Wie viele Punkte ein Raum Maximal abwerfen kann                                                                | Ja        |
| requiredPointsToComplete | integer                   | Wie viele Punkte in einem Raum angesammelt werden müssen, damit er als abgeschlossen gilt                      | Ja        |
| requiredSpacesToEnter    | string (boolsche Algebra) | Spaces mit UND und ODER verknüpft, welche die Bedingung zum öffnen des Space bilden (siehe beschreibung unten) | Ja        |
| spaceName                | string                    | Name des Lernraumes                                                                                            | Ja        |
| spaceDescription     | string   | Beschreibung des Spaces, welche dem Nutzer angezeigt wird                                  | Nein      |
| spaceGoals           | [string] | Einzelne Lernziele des Spaces                                                                  | Nein      |

### Beschreibung der Felder des Elements

| Schlüssel            | Datentyp | Funktion                                                                                     | benötigt? |
| -------------------- | -------- | -------------------------------------------------------------------------------------------- | --------- |
| lmsElementIdentifier | objekt   | Objekt, welches durch Angabe von Typ und Wert ein Elementen im LMS eindeutig bezeichnen kann | Ja        |
| elementId            | int      | id des Lernelements                                                                          | Ja        |
| elementDescription   | string   | Beschreibung des Lernelements                                                                | Nein      |
| elementGoals         | [string] | ziele des Lernelements                                                                       | Nein      |
| elementCategory      | string   | Art des Lernelements (Bild, Text, H5P, etc)                                                  | Ja        |
| elementFileType      | string   | genauer Content des Lernelements (im Falle Bild: Jpeg, PNG, ...)                             | Ja        |
| elementPoints        | integer  | wie viele Punkte gibt das Element (maximal) gibt, wenn es abgeschlossen ist                  | Ja        |
| elementName          | string   | Name des Elements                                                                            | Ja        |

### Beschreibung der Felder des Root Elements

| Schlüssel   | Datentyp     | Funktion                                 | benötigt? |
| ----------- | ------------ | ---------------------------------------- | --------- |
| fileVersion | string       | gibt die Version des AWT Files an        | Ja        |
| amgVersion  | string       | gibt die Version des AMG Tools an        | Nein      |
| author      | string       | Autor der Lernwelt                       | Nein      |
| language    | ["de", "en"] | Sprache der Lernwelt in Country Code ist | Nein      |
| world       | objekt       | beinhaltet alle Daten der Lernwelt       | Ja        |

## JSON Schema

Das File Format ist mittels JSON-Schema definiert. Neben einer eindeutigen Dokumentation aller Typen, Elementen, Schlüsseln und Strukturen kann damit auch eine Validierung der Datei ausgeführt werden.

Ein JSON-Schema zu jeder Version ist in den Entsprechenden Ordnern zu finden.

## Bedingungen für Lernräume mittels Aussagelogik erstellen

Die Bedingungen zwischen Lernräumen gehen über reine Mindestpunkte hinaus. Aus diesem Grund wurde eine Aussagelogik implementiert, welche es ermöglicht, die Bedingungen zwischen Lernräumen zu definieren.

Dabei werden räume mittels UND (^) und ODER (v) verknüpft. Komplexere zusammenhänge können mittels Klammern realisiert werden. Eine Negation oder ähnliches ist nicht möglich, jedoch gilt die allgemeine Regel "Und vor Oder".

Dabei ist zu beachten, dass ein Raum ab dann als abgeschlossen gilt, wenn er seine benötigen Punkte zur Freischaltung erreicht hat.

Eine Beispielhafte Verknüpfung für einen Lernraum ist: `"requiredSpacesToEnter": "(5)v((7)^(4))"`

## Mögliche Werte (Bitte ergänzen)

### lmsElementIdentifier

- type: "idNumber"
  - value: "UUID"
- type: "moduleName"
  - value: "NameDesModulsInMoodle"

### elementCategory

- "image", "text", "h5p" oder "video"

### elementFileType

- "jpeg", "png", "mp4", "h5p" weitere bitte von AMG ergänzen

## Changelog

# Diskussionspunkte

- **identifier für verschiedene Elemente auf dem LMS**: Elemente können auf dem LMS leider nicht immer mit einer Id versehen werden. Daher MUSS es mehrere Möglichkeiten geben, diese zu identifizieren. Bisher werden Elemente im LMS (Lernwelt, Lernelement) mittels dem Namen identifiziert. Da es in Zukunft aber möglich sein soll, diese Elemente mittels einer ID zu identifizieren, wurde sich bis jetzt dazu entschieden, ein Objekt aus Identifikationsmethode und dem jeweiligen Wert zu übergeben.
- **worldGoals**: Im Gegensatz zu bisher ist hier nun ein Array aus Strings. Das hat den Grund, dass es eventuell sinnvoller ist, die Lernziele als Liste zu Formatieren und diese dementsprechend nutzerfreundlicher anzuzeigen. Gleiches gilt für die Spaces, jedoch nicht für Elemente
- **fileVersion**: Soll dazu dienen, den richtigen Parser zu wählen.
- **amgVersion**: Soll primär zum debugging dienen, um zu identifizieren, mit welchem Generator das File erstellt wurde, sollte etwas schief gegangen sein.
- **elementPoints**: Ursprünglich war hier eine Liste an "Belohnungen", welche der Nutzer für das Abschließen des Elements bekommt. Neben Punkten hätten dies beispielsweiße auch Badges oder Errungenschaften sein können. Jedoch wurde sich dazu entschieden, Errungenschaften und Badges vorerst zu Ignorieren und diese, wenn immer noch gefordert an anderer Stellt zu implementieren.
- **elementContent**: Wurde entfernt, da der content des Lernelements ohnthin über die API erfragt wird, da nicht in jeden fall der content hier angegeben werden kann.
- **elementFileType**: Gibt an, wie der Inhalt des Element behandelt werden muss, sobald er von dem 3D Frontend geladen wurde.
- **learningSpaceParentId**: Wurde entfernt, da es eine redundante Information ist und nicht nicht Normalisiert ist.
- **included Points**: Wie viel Punkte ein Raum maximal abwerfen kann. Das ist halt so von dem alten drin. Könnte allerdings entfernt werden, da redundate Information
- **Autor und Language** Da es im AMG ohnehin eingegeben wird, schlage ich vor, das direkt auch mitzuliefern. Es wurde sich auch überlegt, den moodle Autor dafür zu nutzen, allerdings kann es auch ein anderer sein, der das Backup auf Moodle hochlädt.
- **namen für alle elemente** Es wurden Namen für alle Elemente hinzugefügt (World, Space, Element)
- **"Learning" als prefix herausgestrichen** der übersicht halber wurde das Wort "Learning" weg rationalisiert

# Breaking changes zu der undokumentierten Version

- `identifier` ist kein Array zudem gibt es nicht mehr den typen "fileName", sondern er heißt jetzt "moduleName" außerdem heißt er jetzt `lmsElementIdentifier`
- `description` in der Welt, den Spaces und Elementen wurde in `worldDescription`, `spaceDescription` und `elementDescription` umbenannt
- `goals` in der Welt, den Spaces und Elementen wurde in `worldGoals`, `spaceGoals` und `elementGoal` umbenannt
- `worldGoals` ist ein Array (auf jeden fall als Diskussionsgrundlage)
- `spaceGoals` ist ebenfalls ein Array (auf jeden fall als Diskussionsgrundlage)
- `requirements` heißt jetzt `requiredSpacesToEnter`
- `elementType` hat die Syntax: elementkategorie:elementtyp
- `learningElementValueList` wird durch einen einfachen Integer `elementPoints` ersetzt
- `learningSpaceParentId` ist nicht mehr vorhanden
- `fileVersion` zwingend benötigt (oder ist wird auf ein default Seites backend und 3D zurück gefallen)
- `included points` ist raus geflogen, da es redundant ist und im 3D nicht benötigt wird
