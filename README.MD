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

| Schlüssel           | Datentyp                  | Funktion                                                                                                       |
| ------------------- | ------------------------- | -------------------------------------------------------------------------------------------------------------- |
| fileVersion         | string                    | gibt die Version des AWT Files an                                                                              |
| amgVersion          | string                    | gibt die Version des AMG Tools (nicht required) an                                                             |
| world               | objekt                    | beinhaltet alle Daten der Lernwelt                                                                             |
| lmsIdentifier       | objekt                    | Objekt, welches durch Angabe von Typ und Wert ein Elementen im LMS eindeutig bezeichnen kann                   |
| worldDescription    | string                    | Beschreibung der Lernwelt, welche dem Nutzer angezeigt wird (nicht required)                                   |
| worldLearningGoals  | [string]                  | Einzelne Lernziele der Welt (nicht required)                                                                   |
| spaceContent        | [int]                     | ids der LernRäume, welche sich in der Welt befinden                                                            |
| includedPoints      | int                       | Wie viele Punkte ein Raum Maximal abwerfen kann                                                                |
| requiredPoints      | integer                   | Wie viele Punkte in einem Raum angesammelt werden müssen, damit er als abgeschlossen gilt                      |
| requiredSpaces      | string (boolsche Algebra) | Spaces mit UND und ODER verknüpft, welche die Bedingung zum öffnen des Space bilden (siehe beschreibung unten) |
| elementId           | int                       | in des LernElements                                                                                            |
| elementDescripiotn  | string                    | Beschreibung des Lernelements (nicht required)                                                                 |
| elementLearningGoal | string                    | ziel des Lernelements (nicht required)                                                                         |
| elementCategory     | string                    | Art des Lernelements (Bild, Text, H5P, etc)                                                                    |
| elementContentType  | string                    | genauer Content des Lernelements (im Falle Bild: Jpeg, PNG, ...)                                               |
| elementPoints       | integer                   | wie viele punkte gibt das Element (maximal) wenn es abgeschlossen ist                                          |
| author              | string                    | Autor der Lernwelt (nicht required)                                                                            |
| language            | string ["de", "en"]       | Sprache der Lernwelt in Country Code ist (nicht required)                                                      |

## JSON Schema

Das File Format ist mittels JSON-Schema definiert. Neben einer eindeutigen Dokumentation aller Typen, Elementen, Schlüsseln und Strukturen kann damit auch eine Validierung der Datei ausgeführt werden.

Ein JSON-Schema zu jeder Version ist in den Entsprechenden Ordnern zu finden.

## Bedingungen für Lernräume mittels Aussagelogik erstellen

Die Bedingungen zwischen Lernräumen gehen über reine Mindestpunkte hinaus. Aus diesem Grund wurde eine Aussagelogik implementiert, welche es ermöglicht, die Bedingungen zwischen Lernräumen zu definieren.

Dabei werden räume mittels UND (^) und ODER (v) verknüpft. Komplexere zusammenhänge können mittels Klammern realisiert werden. Eine Negation oder ähnliches ist nicht möglich, jedoch gilt die allgemeine Regel "Und vor Oder".

Dabei ist zu beachten, dass ein Raum ab dann als abgeschlossen gilt, wenn er seine benötigen Punkte zur Freischaltung erreicht hat.

Eine Beispielhafte Verknüpfung für einen Lernraum ist: `"requiredSpaces": "(5)v((7)^(4))"`

### Mögliche Werte (Bitte ergänzen)

#### lmsIdentifier

- type: "idNumber"
  - value: "UUID"
- type: "moduleName"
  - value: "NameDesModulsInMoodle"

#### elementCategory

- "image", "text", "h5p" oder "video"

#### elementContentType

- "image:jpeg", "image:png", "video:mp4", "h5p" weitere bitte von AMG ergänzen

## Changelog

# Diskussionspunkte

- **identifier für verschiedene Elemente auf dem LMS**: Elemente können auf dem LMS leider nicht immer mit einer Id versehen werden. Daher MUSS es mehrere Möglichkeiten geben, diese zu identifizieren. Bisher werden Elemente im LMS (Lernwelt, Lernelement) mittels dem Namen identifiziert. Da es in Zukunft aber möglich sein soll, diese Elemente mittels einer ID zu identifizieren, wurde sich bis jetzt dazu entschieden, ein Objekt aus Identifikationsmethode und dem jeweiligen Wert zu übergeben.
- **worldLearningGoals**: Im Gegensatz zu bisher ist hier nun ein Array aus Strings. Das hat den Grund, dass es eventuell sinnvoller ist, die Lernziele als Liste zu Formatieren und diese dementsprechend nutzerfreundlicher anzuzeigen.
- **fileVersion**: Soll dazu dienen, den richtigen Parser zu wählen.
- **amgVersion**: Soll primär zum debugging dienen, um zu identifizieren, mit welchem Generator das File erstellt wurde, sollte etwas schief gegangen sein.
- **elementPoints**: Ursprünglich war hier eine Liste an "Belohnungen", welche der Nutzer für das Abschließen des Elements bekommt. Neben Punkten hätten dies beispielsweiße auch Badges oder Errungenschaften sein können. Jedoch wurde sich dazu entschieden, Errungenschaften und Badges vorerst zu Ignorieren und diese, wenn immer noch gefordert an anderer Stellt zu implementieren.
- **elementContent**: Wurde entfernt, da der content des Lernelements ohnthin über die API erfragt wird, da nicht in jeden fall der content hier angegeben werden kann.
- **elementContentType**: Gibt an, wie der Inhalt des Element behandelt werden muss, sobald er von dem 3D Frontend geladen wurde.
- **learningSpaceParentId**: Wurde entfernt, da es eine redundante Information ist und nicht nicht Normalisiert ist.
- **included Points**: Wie viel Punkte ein Raum maximal abwerfen kann. Das ist halt so von dem alten drin. Könnte allerdings entfernt werden, da redundate Information

# Breaking changes zu der undokumentierten Version

- `identifier` ist kein Array zudem gibt es nicht mehr den typen "fileName", sondern er heißt jetzt "moduleName" außerdem heißt er jetzt `lmsIdentifier`
- `description` wurde in `worldDescription` umbenannt gleiches gilt für Elemente
- `goals` der Lernwelt wurde in `worldLearningGoals` umbenannt gleiches gilt für Elemente
- `worldGoals` ist ein Array (auf jeden fall als Diskussionsgrundlage)
- `requirements` heißt jetzt `requiredSpaces`
- `elementType` hat die Syntax: elementkategorie:elementtyp
- `learningElementValueList` wird durch einen einfachen Integer `elementPoints` ersetzt
- `learningSpaceParentId` ist nicht mehr vorhanden
- `fileVersion` zwingend benötigt (oder ist wird auf ein default Seites backend und 3D zurück gefallen)
