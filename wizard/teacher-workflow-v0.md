# Teacher Workflow V0

Status: funktionaler UI-Contract für den Wizard-Prototyp
Stand: 02.07.2026

## Zweck

Dieses Dokument beschreibt nicht das visuelle Design. Es legt fest, wann,
wo und welche Informationen Lehrende im Wizard angeben können und welche
Validierungen wann greifen müssen.

Der UI-Designer hat Freiheit bei Layout, Komponenten, Navigation, Icons,
Microcopy und Interaktionsdetails. Fest sind nur:

- welche fachlichen Daten erfasst werden müssen,
- welche Daten die UI automatisch ableitet,
- welche Fehler blockieren,
- wann der Paket-erstellen-Button erlaubt ist,
- welche Bausteine im aktuellen Generator-Slice wirklich generierbar sind,
- dass technische Interna wie Tokens, Petri-Netze und Generator-Actions nicht
  als zentrale UI-Begriffe erscheinen.

## Beispiel-Workflow Aus Lehrenden-Sicht

Eine Lehrkraft möchte einen kleinen Escape Room erstellen:

1. Sie legt Titel, Sprache, Zielgruppe, Spielerzahl und Zeitlimit fest.
2. Sie beschreibt die Spielrolle, Ausgangslage und Mission.
3. Sie fügt Rätselbausteine hinzu, z. B. Computer-Login, E-Mail-Auswahl,
   Keypad und finale Tür.
4. Die UI zeigt daraus entstehende Oberflächen wie Computer, Keypad und Tür.
5. Sie ordnet die Rätsel in einer strukturierten Ablaufansicht an.
6. Sie füllt pro Baustein die benötigten Inhalte aus.
7. Sie ergänzt Texte, Bilder, Audio und optionale Hinweise.
8. Die UI prüft den Raum auf blockierende Fehler.
9. Wenn keine blockierenden Fehler existieren, erstellt sie ein `deer.zip`.
10. Danach kann dieses Paket geteilt oder für V0 manuell an den Generator
    übergeben werden. Bequemere One-Click-Verpackung folgt später.

## Workflow-Schritte

### 1. Übersicht

Zweck: Status und nächste offene Aufgaben sichtbar machen.

Eingaben:

- keine Pflichtangaben.

Anzeigen:

- Projekttitel, falls vorhanden,
- Fortschritt je Schritt,
- Anzahl Rätsel,
- offene Pflichtfelder,
- blockierende Fehler,
- Warnungen,
- Paket-Status.

Validierung:

- keine eigene Validierung,
- aggregiert nur den Zustand der anderen Schritte.

### 2. Rahmen

Zweck: Allgemeine Sitzungsdaten erfassen.

Pflichtangaben:

- Raumtitel,
- Sprache,
- Zielgruppe,
- Vorwissen,
- minimale und maximale Spielerzahl,
- Zeitlimit,
- Zeitmodus: hartes oder weiches Limit.

Validierung:

- Textpflichtfelder dürfen nicht leer sein.
- Spielerzahl: `1 <= min <= max`.
- Zeitlimit muss eine positive Zahl sein.
- Zeitmodus ist `hard` oder `soft`.
- Sprache ist für V0 standardmäßig `de-DE`. Weitere Sprachen können später
  folgen.

Blockiert Paket-Erstellung, wenn:

- Pflichtfeld fehlt,
- Zahlenbereich ungültig ist.

Nicht als Eingabe sichtbar:

- empfohlene Spielerzahl,
- Kooperationsmodus; V0 ist immer kooperativ.

### 3. Szenario

Zweck: Sicherstellen, dass der Escape Room eine Spielsituation hat und nicht nur
Fachinhalt in Spieloberflächen verpackt.

Pflichtangaben:

- Rolle der Spielenden,
- Ausgangslage,
- Mission,
- Intro-Text,
- Erfolgstext,
- Fehlschlagtext.

Optionale Angaben:

- Lore-Texte,
- Lore-Bild,
- Intro- oder Ambient-Audio.

Validierung:

- Pflichttexte dürfen nicht leer sein.
- Mission sollte ein klares Spielziel enthalten.

Blockiert Paket-Erstellung, wenn:

- Pflichttext fehlt.

Warnungen:

- sehr lange Texte,
- Mission wirkt unklar,
- Text beschreibt nur Fachinhalt ohne Spielsituation.

### 4. Bausteine Und Oberflächen

Zweck: Lehrende wählen fachliche Spielbausteine. Die UI leitet daraus
benötigte Oberflächen ab.

Auswählbare V0-Bausteine:

- Stromschalter / `state_change.confirm`,
- Fund / `collection`,
- Computer-Login / `input.credentials`,
- E-Mail- oder Optionsauswahl / `choice.email_list`,
- Decoding-Eingabe / `input.decoded_text`,
- Keypad / `input.numeric`,
- USB verwenden / `item_use`,
- Control Panel / `control_panel`,
- Bildfragmente / `assembly.image_fragments`.

Abgeleitete Oberflächen:

- Computer,
- Keypad,
- Tür,
- Weltobjekt oder Fundort,
- Inventarziel,
- Fragmentbereich,
- Control Panel.

UI-Regel:

- Lehrende legen nicht zuerst eine technische Slot-Liste an.
- Wenn ein Baustein eine Oberfläche braucht, erzeugt die UI sie automatisch
  oder bietet eine passende vorhandene Oberfläche zur Auswahl an.
- Intern schreibt die UI diese Oberflächen in das `surfaces`-Array der
  `deer.json`; Rätselparameter referenzieren sie über `surfaceId`.
- Wenn mehrere Oberflächen desselben Typs existieren, muss die UI eine
  eindeutige Auswahl oder Benennung erlauben.

Validierung:

- Jeder Baustein muss eine kompatible Oberfläche haben.
- Die UI darf keine Oberflächen anbieten, die zum Baustein nicht passen.
- Wiederverwendete Oberflächen, z. B. Computer, müssen eindeutig referenziert
  werden.

Blockiert Paket-Erstellung, wenn:

- ein Baustein keine benötigte Oberfläche hat,
- eine inkompatible Baustein-/Oberflächen-Kombination entsteht.
- ein Baustein im aktuellen Generator-Slice noch nicht generierbar ist.

### 5. Rätselablauf

Zweck: Festlegen, welche Rätsel in welcher Reihenfolge gelöst werden müssen.

Empfohlenes fachliches Modell:

- Der Ablauf besteht aus Gruppen.
- Gruppen werden nacheinander gelöst.
- Eine Gruppe kann ein oder mehrere Rätsel enthalten.
- Mehrere Rätsel in derselben Gruppe gelten als parallel lösbar.
- Die nächste Gruppe wird erst relevant, wenn alle Progressionsrätsel der
  vorherigen Gruppe lösbar abgeschlossen werden können.

Der UI-Designer kann das als Liste, Timeline, Board, Kartenansicht oder Canvas
umsetzen. Wichtig ist nur, dass daraus eindeutig eine gültige Reihenfolge mit
optionalen Parallelgruppen abgeleitet werden kann.

Mindestoperationen:

- Rätsel hinzufügen,
- Rätsel entfernen,
- Rätsel umbenennen,
- Rätsel in eine andere Gruppe verschieben,
- Reihenfolge von Gruppen ändern,
- Parallelgruppe erstellen,
- Parallelgruppe wieder auflösen.

Nicht erforderlich für V0:

- frei gezogene beliebige Graphkanten,
- optionale Rätselpfade,
- alternative Enden,
- mehrere Level.

Validierung:

- Es gibt mindestens ein Progressionsrätsel.
- Jedes Progressionsrätsel liegt in genau einer Ablaufgruppe.
- Keine leeren Gruppen.
- Keine Zyklen.
- Kein Progressionsrätsel ist unerreichbar.
- Kein Progressionsrätsel kann übersprungen werden.
- Der Endzustand ist erreichbar.

Blockiert Paket-Erstellung, wenn:

- Softlock möglich ist,
- ein Rätsel unerreichbar ist,
- ein Progressionsrätsel übersprungen werden kann,
- eine Abhängigkeit zyklisch oder unlösbar ist,
- der Endzustand nicht erreicht werden kann.

### 6. Rätsel Bearbeiten

Zweck: Die konkreten Inhalte und Lösungen pro Baustein erfassen.

Gemeinsame Pflichtangaben pro Rätsel:

- sichtbarer Name,
- Bausteintyp,
- Aufgabe für Spielende,
- Schwierigkeit,
- geschätzte Dauer,
- Erfolg/Freischaltung aus kontrollierter Auswahl,
- benötigte Inhalte oder Assets.

Typ-spezifische Pflichtangaben:

| Baustein | Pflichtangaben |
|---|---|
| Stromschalter | Zielobjekt, Bestätigungstext, Erfolgstext |
| Fund | Fundtyp, Fundort, Reward oder Ressource |
| Computer-Login | Felder, Labels, erwartete Werte |
| E-Mail-Auswahl | Optionen, Inhalte, genau richtige Option |
| Decoding-Eingabe | kodierter Wert, erwartete Antwort, Decoding-Schritte |
| Keypad | Zahlencode, maximale Länge, Erfolg |
| USB verwenden | Kandidaten-Items, korrektes Item, Fehlerverhalten |
| Control Panel | Controls, Zielzustand, Freischaltung |
| Bildfragmente | Ausgangsbild, Fragmentanzahl, Ergebnisressource |

Validierung:

- Typ-spezifische Pflichtfelder müssen vorhanden sein.
- Kontrollierte Auswahlfelder dürfen nur valide Optionen anbieten.
- Lösungsfelder müssen zum Eingabetyp passen, z. B. nur Ziffern beim Keypad.
- Retry muss möglich bleiben, wenn eine falsche Eingabe oder Auswahl nicht
  sofort beendet.
- Erfolg/Freischaltung darf keinen unlösbaren Ablauf erzeugen.

Blockiert Paket-Erstellung, wenn:

- Pflichtparameter fehlen,
- Lösung nicht zum Eingabetyp passt,
- Erfolg/Freischaltung mit dem Ablauf unvereinbar ist.

### 7. Inhalte, Assets Und Hinweise

Zweck: Texte, Bilder, Audio und optionale Hilfen zentral verwalten.

Erlaubt in V0:

- Texte direkt im Wizard,
- Bilder als Upload,
- Audio als Upload,
- optionale Hinweise pro Rätsel.

Nicht erlaubt in V0:

- Custom-Themes,
- Tilesets,
- Sprites,
- UI-Skins,
- beliebige Office- oder PDF-Dateien als Runtime-Dokumente.

Validierung:

- Referenzierte Assets müssen existieren.
- Asset-Typ muss unterstützt sein.
- Required Assets müssen vorhanden sein.
- Hinweise sind optional, aber als leeres Array modellierbar.
- Hint-Freischaltungen müssen auf existierende Rätsel, Ressourcen oder
  Oberflächen verweisen.

Blockiert Paket-Erstellung, wenn:

- required Asset fehlt,
- Asset nicht unterstützt wird,
- ein Rätsel auf eine nicht vorhandene Ressource verweist.
- ein Hint auf eine nicht vorhandene Freischaltbedingung verweist.

Warnungen:

- Rätsel ohne Hinweise,
- ungenutzte Assets,
- sehr lange Texte.

Hint-Freischaltungen:

- ohne Bedingung: sofort verfügbar,
- nach Zeit,
- nach Fehlversuchen,
- nach gelesener Ressource,
- nach besuchter Oberfläche,
- nach gelöstem Rätsel.

### 8. Prüfen Und Paket Erstellen

Zweck: Letzte nicht-technische Prüfung vor dem Erstellen des `deer.zip`.

Anzeigen:

- alle blockierenden Fehler,
- alle Warnungen,
- Anzahl Rätsel,
- geschätzte Gesamtdauer,
- verwendete Bausteine,
- verwendete Assets.

Hauptaktion:

- `Paket erstellen`.

Validierung:

- Schema-Preflight,
- Pflichtfelder,
- Bausteinparameter,
- Oberflächen,
- Asset-Referenzen,
- Ablauf/Softlock-Prüfung.

Paket-Erstellung ist nur erlaubt, wenn:

- keine blockierenden Fehler existieren.

Warnungen blockieren nicht.

## Validierungszeitpunkte

| Zeitpunkt | Zweck | Beispiel |
|---|---|---|
| Direkt am Feld | schnelle Rückmeldung | leerer Titel, ungültige Spielerzahl |
| Beim Verlassen eines Schritts | Schrittstatus setzen | Szenario unvollständig |
| Nach Bausteinänderung | abgeleitete Oberflächen prüfen | Keypad braucht Keypad-Oberfläche |
| Nach Ablaufänderung | Softlocks und Skips verhindern | Rätsel wird unerreichbar |
| Vor Paket-Erstellung | finaler Preflight | Schema, Assets, Graph, Pflichtparameter |

## Fehlerstufen

### Blockierend

Blockierend ist alles, was zu einem technisch oder spielerisch kaputten Raum
führen kann:

- fehlende Pflichtdaten,
- fehlende required Assets,
- inkompatible Baustein-/Oberflächen-Kombination,
- unerreichbares Rätsel,
- ungewollt überspringbares Progressionsrätsel,
- Softlock,
- zyklische oder unlösbare Abhängigkeit,
- nicht erreichbarer Endzustand.

### Warnung

Warnungen helfen bei Qualität, blockieren aber nicht:

- lange Texte,
- keine Hinweise,
- schwache Story-Einbettung,
- ungenutzte Assets,
- geschätzte Dauer passt schlecht zum Zeitlimit.

## Automatisch Abgeleitete Daten

Die UI darf und sollte technische Daten automatisch erzeugen:

- stabile IDs,
- `surfaces` aus den gewählten Bausteinen,
- interne Tokens,
- technische `successEffect`-Werte,
- Slot-Typen,
- leere Arrays für `resources`, `hints` und `assetIds`,
- Standardwerte für Theme und Levelanzahl.

Lehrende sollen diese Werte nicht direkt bearbeiten müssen.

## Gestaltungsspielraum Für Die UI

Frei wählbar:

- Layout und visuelle Hierarchie,
- konkrete Komponenten,
- Drag-and-drop oder Buttons,
- Karten-, Listen-, Board- oder Canvas-Darstellung,
- Icons, Farben und Microcopy,
- ob Schritte strikt nacheinander oder frei anwählbar sind.

Nicht frei:

- Paket-Erstellung darf nur bei gültigem Preflight aktiv sein.
- Technische Interna dürfen nicht zur Hauptsprache der UI werden.
- Ablauf muss in eine valide `deer.json` übersetzbar sein.
- V0 darf keine optionalen Progressionsrätsel oder alternative Enden erzeugen.
