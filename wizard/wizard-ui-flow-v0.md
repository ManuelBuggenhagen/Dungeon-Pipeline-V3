# Wizard UI Flow V0 Draft

Stand: 02.07.2026
Status: UI- und Eingabevorschlag zur Diskussion

## Ziel

Dieses Dokument beschreibt, wie der Wizard für Lehrende aussehen soll und
welche Angaben in V0 wirklich gemacht werden müssen. Generator-Details,
Petri-Netze, Tokens und technische Runtime-Interna sollen in der UI nicht im
Vordergrund stehen.

Der konkrete Lehrenden-Workflow als UI-Contract steht in
[`teacher-workflow-v0.md`](teacher-workflow-v0.md). Dieses Dokument hier bleibt
die kompakte Schrittübersicht.

Der Wizard soll Lehrende durch eine strukturierte Authoring-Oberfläche führen:

```text
Rahmen festlegen
-> Szenario beschreiben
-> Rätselbausteine und daraus entstehende Oberflächen planen
-> Rätselablauf konfigurieren
-> Inhalte, Assets und Hinweise ergänzen
-> Validieren und deer.zip erstellen
```

## UI-Grundsätze

- Der Wizard ist eine separate Browser-/Standalone-Oberfläche.
- Lehrende bearbeiten keine JSON-Datei direkt.
- Technische Begriffe wie Token, Petri-Netz oder Generator-Action werden in der
  UI durch fachliche Begriffe ersetzt.
- Der Paket-erstellen-Button ist erst aktiv, wenn der Client-Preflight
  gültig ist.
- Fehler werden am betroffenen Schritt, Rätsel oder Feld angezeigt.
- Warnungen dürfen sichtbar bleiben, blockieren aber nicht.
- V0 nutzt ein Standard-Theme; Custom-Themes, Tilesets, Sprites und UI-Skins
  werden nicht abgefragt.
- V0 fragt keine Lernziele, Evaluation, Debriefing, Telemetrie oder
  Pre-/Post-Tests ab.
- Der Wizard startet ohne vorausgewählten Raum. Lehrende bauen den Escape Room
  von Grund auf neu.
- V0 bietet nur die aktuell vorhandenen, aus The Last Hour ableitbaren
  Spiel-Elemente als Bausteine an.
- Der UI-Prototyp darf Bausteine zeigen, die im Konzept vorgesehen sind. Der
  Paket-Flow darf aber nur aktiv werden, wenn alle verwendeten Bausteine vom
  aktuellen Generator-Slice unterstützt werden.
- Deaktivierte Bausteine müssen sichtbar begründen, warum sie nicht nutzbar
  sind.

## Hauptnavigation

Empfohlene Schritte:

1. **Übersicht**
2. **Rahmen**
3. **Szenario**
4. **Raum & Oberflächen**
5. **Rätselablauf**
6. **Rätsel bearbeiten**
7. **Inhalte & Assets**
8. **Prüfen & Paket Erstellen**

Die linke Navigation sollte jeden Schritt mit Status markieren:

- `leer`
- `unvollständig`
- `gültig`
- `Warnung`
- `Fehler`

## 1. Übersicht

Zweck: Projektstatus sichtbar machen und die nächsten offenen Aufgaben zeigen.

Pflichtangaben: keine.

Anzeigen:

- Raumtitel
- Fortschritt der Wizard-Schritte
- Anzahl Rätsel
- offene Pflichtfelder
- blockierende Fehler
- Warnungen
- letzter gültiger Preflight-Status

Aktionen:

- weiter zum nächsten offenen Schritt
- Entwurf speichern
- Paket erstellen, wenn alles gültig ist

## 2. Rahmen

Zweck: Allgemeine Sitzungsdaten, die der Raum braucht.

Pflichtfelder:

| UI-Feld | Interne Bedeutung | Validierung |
|---|---|---|
| Raumtitel | `metadata.title` | nicht leer |
| Sprache | `metadata.locale` | V0 default: `de-DE`; weitere Sprachen später |
| Zielgruppe | `session.targetAudience` | nicht leer |
| Vorwissen | `session.priorKnowledge` | darf kurz sein, aber nicht leer |
| Spielerzahl min/max | `session.playerCount` | `1 <= min <= max` |
| Zeitlimit | `session.time.limitMinutes` | positive Zahl |
| Zeitmodus | `session.time.limitMode` | `hard` oder `soft` |

Nicht sichtbar oder fest:

- Theme: V0 immer Standard-Theme.
- Levelanzahl: V0 immer ein Level.
- Kooperationsmodus: V0 immer kooperativ.

Zeitmodus:

- `hard`: Nach Ablauf endet der Raum.
- `soft`: Nach Ablauf darf weitergespielt werden; Hinweise oder Unterstützung
  dürfen stärker werden.

## 3. Szenario

Zweck: Story-Rahmen, damit der Raum nicht wie eine Vorlesungsfolie im Spiel
wirkt.

Pflichtfelder:

| UI-Feld | Interne Bedeutung | Validierung |
|---|---|---|
| Rolle der Spielenden | `scenario.playerRole` | nicht leer |
| Ausgangslage | `scenario.premise` | kurzer Fließtext |
| Mission | `scenario.mission` | klares Ziel |
| Intro-Text | `scenario.introText` | nicht leer |
| Erfolgstext | `scenario.successText` | nicht leer |
| Fehlschlagtext | `scenario.failureText` | nicht leer |

Optionale Felder:

- ein bis drei Lore-Texte
- optionales Lore-Bild
- optionales Intro-/Ambient-Audio

Client-Warnungen:

- Text sehr lang
- Mission ist unklar formuliert
- Intro beschreibt nur Fachinhalt, aber keine Spielsituation

## 4. Raum & Oberflächen

Zweck: Sichtbar machen, welche Interaktionsorte aus den gewählten Bausteinen
entstehen. Lehrende sollen keine technische Slot-Struktur vorab planen.

V0 startet nicht mit vorausgewählten Oberflächen. Oberflächen werden aus den
gewählten Rätselbausteinen abgeleitet. Wenn Lehrende z. B. ein
Computer-Login-Rätsel anlegen, erzeugt die UI daraus einen benötigten Computer
und fragt nur noch die fachlich sichtbaren Eigenschaften ab.

Mögliche abgeleitete Oberflächen:

| Oberfläche | Sichtbarer Name | Zweck |
|---|---|---|
| `computer_main` | Labor-PC | Login, E-Mails, Browser, Dateien, USB, Control Panel |
| `keypad_storage` | Storage-Keypad | Zahlencode für Storage |
| `door_storage` | Storage-Tür | durch Keypad oder Control Panel öffnen |
| `door_exit` | Ausgangstür | final öffnen |
| `vent_main` | Lüftung | Seriennummer und Papierfragmente |
| `trash_slots` | Papierkörbe | Funde und Trash-Minispiel |
| `container_slots` | Container/Schreibtische/Regale | Hinweise und Items |
| `assembly_area` | Fragmentbereich | Bildfragmente zusammensetzen |

Pflichtangaben entstehen aus den gewählten Rätseln:

| UI-Feld | Bedeutung | Validierung |
|---|---|---|
| mindestens ein Computer | für computernahe Rätsel | vorhanden, wenn Computer-Rätsel genutzt werden |
| mindestens ein Keypad-Slot | für Keypad-Rätsel | vorhanden, wenn `input.numeric` als Keypad genutzt wird |
| mindestens ein Container/Fundort | für `collection` | vorhanden, wenn Fund-Rätsel genutzt werden |
| mindestens ein Assembly-Bereich | für Fragmente | vorhanden, wenn `assembly.image_fragments` genutzt wird |

Lehrende wählen primär Bausteine wie "Computer-Login", "Keypad" oder
"Control Panel". Die UI leitet daraus die benötigten Oberflächen ab und zeigt
sie zur Kontrolle an. Wenn eine Eingabe wie Passwort oder Code benötigt wird,
kann die UI anbieten, den Wert automatisch vorzuschlagen oder manuell
festzulegen.

Intern schreibt die UI die abgeleiteten Oberflächen in `surfaces`. Die
fachliche Auswahl "Labor-PC" wird im JSON z. B. zu einer `surfaceId` wie
`s_main_computer`. Lehrende müssen diese IDs nicht sehen.

## 5. Rätselablauf

Zweck: Festlegen, welche Rätsel in welcher Reihenfolge gelöst werden müssen.

Empfohlenes UI-Modell:

- strukturierte Ablauf-Liste mit optionalen Parallelgruppen
- jedes Rätsel als Karte
- Abhängigkeiten als "danach freigeschaltet"
- keine sichtbaren Token-Namen für Lehrende
- ein Canvas kann später eine alternative Visualisierung sein, sollte aber
  nicht die erste Bedienlogik erzwingen

Pflichtangaben pro Knoten:

| UI-Feld | Interne Bedeutung | Validierung |
|---|---|---|
| Rätselname | `riddle.title` | eindeutig genug |
| Baustein-Typ | `riddle.type` | V0-Typ |
| Kurzaufgabe | `playerFacingTask` | nicht leer |
| Vorgänger | `requiresTokens` indirekt | darf keinen Zyklus erzeugen |
| Ergebnis/Freischaltung | `producesTokens` indirekt | muss zu späterem Schritt passen |

V0-Regeln:

- Alle Progressionsrätsel müssen auf einem durchspielbaren Pfad liegen.
- Keine optionalen Rätsel.
- Branches dürfen Parallelität ausdrücken, aber keine Rätsel überspringen.
- Der Wizard zeigt Fehler sofort im Graphen.
- Der Editor verhindert oder markiert Zyklen, unerreichbare Knoten und
  Abhängigkeiten, die erst nach dem benötigten Rätsel verfügbar werden.
- Frei editierbar bedeutet in V0 nicht beliebig: Der Graph darf kreativ
  angeordnet werden, bleibt aber durch Validierungsregeln eingeschränkt.

## 6. Rätsel Bearbeiten

Zweck: Die konkreten Eingaben für jedes Rätsel erfassen. Die UI zeigt nur die
Felder, die zum gewählten Baustein passen.

### 6.1 Stromschalter / `state_change.confirm`

Pflichtfelder:

- sichtbarer Name
- Aufgabe für Spielende
- Oberfläche/Fundort, meist `world`
- Zielobjekt, z. B. Schalter
- Bestätigungsfrage
- Erfolgstext
- Freischaltung aus kontrollierter Auswahl, z. B. "Computer einschalten"

Optionale Felder:

- Abbrechen-Text
- Sound

### 6.2 Fund / `collection`

Pflichtfelder:

- sichtbarer Name
- Aufgabe für Spielende
- Fundtyp: Container, Weltobjekt, Papierkorb-Minispiel, Computer-Datei
- Fundort/Oberfläche
- Reward oder Ressource
- Freischaltung aus kontrollierter Auswahl

Zusätzlich bei Papierkorb-Minispiel:

- Anzahl Papierobjekte
- Asset für gefundenes Objekt

### 6.3 Computer-Login / `input.credentials`

Pflichtfelder:

- Computer-Oberfläche
- Feldliste
- je Feld: Label, erwarteter Wert, geheim ja/nein
- Erfolg: Computer-Tabs freischalten

V0-Default für The Last Hour:

- Feld 1: E-Mail
- Feld 2: Passwort

### 6.4 E-Mail-Auswahl / `choice.email_list`

Pflichtfelder:

- Computer-Oberfläche
- Liste von E-Mails
- mindestens zwei Optionen
- genau eine korrekte Option
- pro E-Mail: Absender, Absenderadresse, Betreff, Inhalt
- bei Link-Aufgaben: Linktext und URL
- Erfolg: Recovery-/Browser-Seite freischalten

V0-Entscheidung:

- bevorzugt als Computer-Tab
- einfacher Dialog nur als technischer Fallback

### 6.5 Decoding-Eingabe / `input.decoded_text`

Pflichtfelder:

- Oberfläche, meist Computer
- kodierter Wert
- erwartete Antwort
- Decoding-Schritte, z. B. Binary -> Hex -> ASCII
- Ressourcen, die die Decoding-Schritte erklären
- Erfolg: Dokument oder nächstes Rätsel freischalten

### 6.6 Keypad / `input.numeric`

Pflichtfelder:

- Keypad-Oberfläche
- erwarteter Zahlencode
- maximale Länge
- Erfolg: Tür öffnen oder Bereich freischalten

Optionale Felder:

- falsche Eingabe Feedback
- Ziffernanzahl anzeigen ja/nein

### 6.7 USB Verwenden / `item_use`

Pflichtfelder:

- Computer-Oberfläche
- Zielobjekt, meist PC
- Liste verfügbarer USB-Sticks
- welcher Stick korrekt ist
- Verhalten bei falschem Stick
- Erfolg: USB-Laufwerk oder Control-Panel-Zugang freischalten

The-Last-Hour-Default:

- mehrere farbige USB-Sticks
- blauer USB ist korrekt
- falscher USB erzeugt `Unknown Device`
- nach kurzer Zeit Reset auf eingeschalteten, ausgeloggten PC
- erneuter Versuch bleibt möglich

### 6.8 Control Panel / `control_panel`

Pflichtfelder:

- Computer-/Panel-Oberfläche
- Liste der Controls
- Abschlusszustand
- Freischaltung bei Abschluss

Pflicht pro Control:

- Label
- Typ: Button, Toggle, Textfeld, Passwortfeld
- erwarteter Wert, falls Eingabefeld
- gesetzter interner Zustand
- benötigte vorherige Panel-Zustände, falls vorhanden

The-Last-Hour-nahe Controls:

- Vent-Seriennummer eingeben
- AC einschalten
- finale Tür mit Passwort entsperren
- finale Tür öffnen

### 6.9 Bildfragmente / `assembly.image_fragments`

Pflichtfelder:

- Ausgangsbild
- Anzahl Fragmente
- Spawn-/Startauslöser, z. B. AC eingeschaltet
- Ergebnis-Ressource, z. B. finales Code-Bild
- Erfolg: finale Information verfügbar machen

V0 nutzt das vorhandene Puzzle-/Item-System.

## 7. Inhalte & Assets

Zweck: Alle Texte, Bilder und Audio-Dateien an einer Stelle verwalten.

Pflichtbereiche:

| Bereich | Pflicht, wenn... |
|---|---|
| Texte | ein Rätsel Text/Lore/Ressource nutzt |
| Bilder | eine Ressource oder Assembly ein Bild nutzt |
| Audio | nur wenn Audio in Szenario oder Feedback aktiviert ist |
| Hinweise | optional, aber pro Rätsel als leeres Array vorhanden |

Hint-Freischaltung:

- sofort verfügbar,
- nach Zeit,
- nach Fehlversuchen,
- nachdem eine Information gelesen wurde,
- nachdem eine Oberfläche/ein Ort besucht wurde,
- nachdem ein Rätsel gelöst wurde.

Die UI sollte diese Bedingungen fachlich benennen. Petri-Net-Tokens bleiben
interne Generator-/Runtime-Details.

V0-erlaubt:

- Text direkt im Wizard
- Bilder als Upload
- Audio als Upload

Nicht V0:

- Themes
- Tilesets
- Sprites
- UI-Skins
- beliebige Office-/PDF-Dateien als Runtime-Dokumente

## 8. Prüfen & Paket Erstellen

Zweck: Lehrende sehen vor dem Erstellen des `deer.zip` eine klare, nicht-technische
Checkliste.

Blockierende Fehler:

- Pflichtfeld fehlt
- Rätsel ohne Fundort/Oberfläche
- benötigte Ressource fehlt
- Asset fehlt
- Rätsel im Ablauf nicht erreichbar
- Progression kann nicht abgeschlossen werden
- Progressionsrätsel kann übersprungen werden
- Softlock oder zyklische Abhängigkeit
- Aktion passt nicht zur gewählten Oberfläche
- Computer-Rätsel ohne Computer
- Keypad-Rätsel ohne Keypad

Warnungen:

- sehr lange Texte
- Rätsel ohne Hinweise
- viele Rätsel ohne klare Story-Einbettung
- erwartete Dauer deutlich höher als Zeitlimit

Hauptaktion:

- `Paket erstellen`

Der Button ist deaktiviert, solange blockierende Fehler existieren.

## Lehrenden-Sicht Auf The Last Hour V0

Der Wizard soll nicht automatisch eine The-Last-Hour-Vorlage anlegen. Diese
Liste beschreibt nur, welche The-Last-Hour-nahen Bausteine in V0 verfügbar sein
sollten, damit ein sinngemäßer Nachbau möglich ist:

1. Strom einschalten
2. Login-Hinweise finden
3. Computer-Login
4. richtige E-Mail erkennen
5. Recovery-Code decodieren
6. Storage-Code aus Dokument entschlüsseln
7. Storage-Keypad öffnen
8. richtigen USB-Stick finden
9. USB am PC verwenden
10. Vent-Seriennummer im Control Panel eintragen
11. AC einschalten
12. Bildfragmente zusammensetzen
13. finale Tür öffnen

Lehrende bauen den Raum selbst aus diesen Bausteinen zusammen. Die UI darf
Vorschläge, Beispiele oder leere Baustein-Karten anbieten, aber keine fertige
Raumstruktur vorauswählen.

## Aktuelle Entscheidungen

1. V0 startet leer. Es gibt keine vorausgewählte The-Last-Hour-Vorlage.
2. V0 nutzt nur die aus The Last Hour ableitbaren Spiel-Elemente als
   verfügbare Bausteine.
3. Der Computer ist kein eigener Hauptschritt, sondern eine Surface, an die
   Rätsel oder Informationen gebunden werden können.
4. Lehrende wählen Oberflächen und Bausteine aus. Werte wie Passwort oder Code
   können automatisch vorgeschlagen oder manuell angegeben werden.
5. Die sichtbare Abschlussaktion in V0 ist `Paket erstellen`. Die `deer.json`
   ist dabei internes Authoring-Modell und Generator-Eingabe, nicht das
   sichtbare Endprodukt für Lehrende.
6. Blockierend sind nur game-breaking Fehler: Softlocks, unerreichbare Rätsel,
   ungewollte Skips, fehlende Pflichtdaten und inkompatible Baustein-/Surface-
   Kombinationen.
7. V0 enthält keine spielbare Preview und keinen Neu-Generieren-Button.
8. Eigenständige Decoy-Rätsel sind nicht Teil von V0. Falsche Optionen,
   falsche Items oder Decoy-Ressourcen innerhalb eines Rätsels bleiben erlaubt.

## Noch Zu Klären

1. Welche Graph-Operationen sind in V0 erlaubt: Karte verschieben, Abhängigkeit
   ziehen, Parallelgruppe erstellen, Reihenfolge per Drag-and-drop?
2. Welche konkrete Komponente nutzt der UI-Prototyp für die strukturierte
   Ablauf-Liste?
3. Wie sichtbar werden Bausteine markiert, die im Konzept existieren, aber vom
   aktuellen Generator-Slice noch nicht unterstützt werden?
