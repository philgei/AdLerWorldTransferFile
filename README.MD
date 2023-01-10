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
Elemente.  Das führt dazu, Elemente, welche öfters in verschiedenen Objekten
vorkommen, in eigene Objekte ausgelagert werden und per Id Referenziert werden. 
### Singular vs Plural
Die Namen von schlüsseln, welche einen einzigen Wert beinhalten, sollen immer im Singular geschrieben werden.
Die Namen von Schlüsseln, welche mehrere Werte (Arrays) beinhalten, sollen immer im Plural geschrieben werden.

## Beschreibung der einzelnen Felder
| Schlüssel            | Datentyp                  | Funktion                                                                                    |
|----------------------|---------------------------|---------------------------------------------------------------------------------------------|
| fileVersion          | string                    | gibt die Version des AWT Files an                                                           |
| amgVersion           | string                    | gibt die Version des AMG Tools an                                                           |
| leanringWorld        | objekt                    | beinhaltet alle Daten der Wernwelt                                                          |
| lmsIdentifier        | objekt                    | Objekt, welches durch angabe von Typ und Wert ein Elmenten im LMS eindeutig bezeichnen kann |
| worldDescription     | string                    | beschreibung der Lernwelt, welche dem Nutzer angezeigt wird                                 |
| worldLearningGoals   | [string]                  | Einzelne Lernziele der Welt                                                                 |
| learningSpaceContent | [int]                     | ids der LernRäume, welche sich in der Welt befinden                                         |
| includedPoints       | int                       | direkt beim Start wird sich in Moodle angemeldet                                            |
| requiredSpaces        | string (boolsche Algebra) | Spaces mit UND und ODER verknüpft, welche die bedingung zum öffnen des Space bilden        |
| elementId            | int                       | in des LernElements                                                                         |
| elementDescription   | string                    | Beschreibung des Lernelements                                                               |
| elementLearningGoal  | string                    | ziel des lernelements                                                                       |
| elementCategory      | string                    | art des Lernelements (Bild, Text, H5P, etc)                                                 |
| elementConentType    | string                    | genauer Content des Lernelements (im Falle bild: Jpeg, PNG, ...)                            |
| elementPoints        | integer                   | wie viele punkte gibt das Element (maximal) wenn es abgeschlossen ist                       |

## JSON Schema
Das File Format ist mittels JSON-Schema definiert. Neben einer eindeutigen Dokumentation aller Typen, Elementen, Schlüsseln und Strukturen kann damit auch eine Validierung der Datei ausgeführt werden. 

Ein JSON-Schema zu jeder Version ist in den Entsprechenden Ordnern zu finden.

TODO: JSON SCHEMA SCHREIBEN^^

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