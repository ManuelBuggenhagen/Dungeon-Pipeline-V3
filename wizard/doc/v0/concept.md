# Wizard Concept V0

Status: aktueller Kurzstand nach Scope-Bereinigung  
Stand: 02.07.2026

## Ziel

Der Wizard ist eine separate, nicht-technische Authoring-Oberfläche für
Lehrende. Er führt durch die Erstellung eines einfachen Educational Escape
Rooms und erzeugt nach erfolgreicher Validierung ein teilbares `deer.zip`.

Die `deer.json` ist das Authoring-Modell:

```text
Wizard UI -> interne deer.json -> deer.zip -> manueller Java-Generator -> Room-Paket
```

## V0-Scope

V0 konzentriert sich auf:

- Rahmenbedingungen: Titel, Sprache, Zielgruppe, Vorwissen, Spielerzahl,
  Zeitlimit und Zeitmodus.
- Szenario: Rolle, Ausgangslage, Mission, Intro, Erfolg und Fehlschlag.
- Rätselablauf: strukturierter Ablauf mit erlaubten Parallelgruppen statt
  beliebig freiem Graph für Lehrende.
- Rätselbausteine: alle aktuell aus The Last Hour ableitbaren Mechaniken.
- Inhalte: Texte direkt im Wizard, Bilder und Audio als Upload.
- Validierung: keine Softlocks, keine unerreichbaren Rätsel, keine ungewollten
  Skips, keine fehlenden Pflichtparameter oder Assets.
- Paket erstellen: `deer.zip` mit `deer.json` und Assets nach erfolgreicher
  Validierung. Für V0 wird dieses Paket als Download bereitgestellt; Import
  bestehender `deer.zip`-Pakete ist nicht Teil von V0.

Nicht V0:

- Lernziele,
- Evaluation,
- Debriefing,
- Telemetrie,
- Pre-/Post-Tests,
- mehrere Themes oder Custom-Themes,
- Zwischeneditor nach dem Generator,
- spielbare Preview,
- Neu-Generieren-Button,
- automatischer Generator-Aufruf aus der Web-App,
- lokaler Backend-Service oder offizieller CLI-Wrapper,
- One-Click-Verpackung als `.jar`, `.exe` oder Installer.

## Authoring-Modell

Lehrende bauen den Escape Room von Grund auf neu. The Last Hour liefert nur die
aktuell vorhandenen Spielbausteine und wiederverwendbare Assets, aber keine
vorausgewählte Raumstruktur.

Oberflächen wie Computer, Keypad, Tür, Weltobjekt oder Fragmentbereich
entstehen aus den gewählten Bausteinen. Die UI darf diese Oberflächen sichtbar
benennen und bei Bedarf konfigurieren, aber Lehrende sollen keine technische
Slot-Liste vorab planen müssen.

Intern schreibt der Wizard diese Oberflächen in ein `surfaces`-Register. Rätsel
referenzieren konkrete Oberflächen über `surfaceId`, damit UI und Generator
nicht mit freien, unvalidierbaren Strings arbeiten.

Für V0 darf der Generator diese Oberflächen noch stark vereinfachen, z. B. nur
eine Level-/World-Surface verwenden oder pro Computer-Baustein einen einfachen
Computer erzeugen. Das Datenmodell bleibt trotzdem die Grundlage für spätere,
genauere Platzierung.

## V0-Bausteine

- `state_change`: einfache Weltaktion, z. B. Stromschalter.
- `collection`: Item, Hinweis oder Ressource finden.
- `input`: Zahlen-, Text-, Login- oder Decoding-Eingabe.
- `choice`: richtige Option auswählen, z. B. E-Mail/URL.
- `item_use`: bestimmtes Item an einem Ziel verwenden, z. B. USB am PC.
- `assembly`: Fragmente zusammensetzen.
- `control_panel`: wiederverwendbare UI mit mehreren Controls.

## Graph-Regeln

Die UI sollte bevorzugt eine strukturierte Darstellung mit Reihenfolge und
Parallelgruppen anbieten. Ein Canvas kann später als alternative Visualisierung
dienen, sollte aber nicht die erste Bedienlogik erzwingen.

Blockierend sind nur Game-Breaking-Probleme:

- ein Rätsel ist nicht erreichbar,
- ein Rätsel kann übersprungen werden, obwohl es Progression ist,
- ein benötigtes Ergebnis wird nie erzeugt,
- eine Abhängigkeit ist zyklisch oder unlösbar,
- ein Endzustand ist nicht erreichbar,
- Pflichtparameter oder Pflichtassets fehlen,
- ein Baustein wird mit einer inkompatiblen Oberfläche kombiniert.

Warnungen dürfen helfen, sollen aber die Paket-Erstellung nicht blockieren.

## Nächster Praktischer Schritt

Der nächste sinnvolle Schritt ist ein UI-Prototyp, der die Schritte aus
`wizard-ui-flow-v0.md` bedienbar macht, daraus eine schema-valide interne
`deer.json` erstellt und ein validiertes `deer.zip` packt. Der Generator wird in
V0 manuell mit diesem Paket oder Projektordner gestartet; eine komfortablere
Integration folgt später.

Für die Umsetzung bleibt `./wizard` der Projekt-Workspace. Die
Konzeptdokumente liegen unter `./wizard/doc/v0`, damit der Root für Web-App,
Paket-Output und spätere Generator-Anbindung frei bleibt. Zusätzlich braucht
Dungeon einen neuen Starter, der erzeugte Wizard-Pakete laden kann.
