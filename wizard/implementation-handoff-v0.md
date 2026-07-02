# Implementation Handoff V0

Status: Arbeitsgrundlage für UI- und Generator-Start
Stand: 02.07.2026

## Ziel

Dieses Dokument ist die Übergabe an die Kollegen, die den Wizard und den
Generator parallel umsetzen. Es ersetzt keine Detailplanung, fixiert aber den
V0-Rahmen so weit, dass beide Seiten starten können.

Leitprinzip für V0:

```text
klein starten -> sauber validieren -> leicht iterierbar bleiben
```

V0 darf weniger Features haben, solange das Datenmodell und die UI-Erwartungen
später erweitert werden können, ohne den Grundvertrag neu zu bauen.

## V0-Produktfluss

```text
Lehrender öffnet Wizard-Web-App
-> füllt Rahmen, Szenario, Rätsel, Inhalte und Hinweise aus
-> Wizard erzeugt intern deer.json
-> Wizard validiert blockierende Fehler
-> Lehrender erstellt ein deer.zip
-> deer.zip enthält deer.json und referenzierte Assets
-> Java-Generator wird für V0 manuell mit diesem Paket/Projekt gestartet
-> Generator validiert erneut und erzeugt das Dungeon-/Room-Paket
```

Nicht in V0:

- spielbare Preview,
- Neu-Generieren-Button,
- Zwischeneditor nach dem Generator,
- automatischer UI-Aufruf des Java-Generators,
- lokaler Backend-Service oder offizieller CLI-Wrapper,
- mehrere Themes,
- Lernziel-/Evaluations-/Debriefing-/Telemetrie-Funktionen,
- frei editierbarer technischer Graph für Lehrende,
- Generator-Seed in `deer.json`.

## Gemeinsamer Contract

Die interne `deer.json` ist der Contract zwischen Wizard-UI und Generator.

Aktuelle Contract-Dateien:

- `deer.schema.json`
- `deer-json-spec.md`
- `examples/deer.example.json`
- `parameter-table-v0.md`

Wichtige Contract-Regeln:

- `formatVersion` bleibt für diesen Draft `0.1-draft`.
- `deer.json` enthält kein `generation`-Objekt.
- `metadata.author` ist optionaler String.
- `session.playerCount` enthält nur `min` und `max`.
- `session.time` enthält `limitMinutes` und `limitMode`.
- `limitMode` ist `hard` oder `soft`.
- V0 nutzt genau ein Standard-Theme.
- `surfaces` ist das interne Register der Oberflächen, die die UI aus den
  Bausteinen ableitet.
- Rätselparameter referenzieren konkrete Oberflächen mit `surfaceId`.
- Die UI zeigt keine Tokens, Petri-Netze oder Generator-Actions als
  Hauptsprache.
- Hints nutzen `severity` und optional `unlock`.
- Hint-Unlock-Bedingungen müssen auf existierende Rätsel, Ressourcen,
  Oberflächen oder interne Tokens zeigen.
- Eigenständige Decoy-Rätsel sind nicht Teil von V0. Decoys dürfen als
  Ressourcen, falsche Optionen oder falsche Items innerhalb eines echten
  Rätsels vorkommen.
- `successEffect` beschreibt intern, welcher kontrollierte Effekt nach einem
  gelösten Rätsel passiert. Freitext-Effekte sind nicht erlaubt.

## Empfohlener Erster Foundation-Slice

Der erste technische Slice ist kein Wegwerf-PoC. Er soll klein genug sein, um UI
und Generator früh end-to-end zu verbinden, aber schon die späteren
Grundentscheidungen verwenden: `deer.json`, `deer.zip`, Validierung,
Rätselgraph, Assets, `surfaces` und kontrollierte Effekte.

Minimaler Slice:

```text
Rahmen
-> Szenario
-> Fund
-> Keypad
-> Tür öffnen
-> deer.zip erstellen
```

Bausteine im ersten Slice:

- `collection.single`
- `input.numeric`
- einfacher `successEffect`, z. B. Tür öffnen
- optionale Hints ohne komplexe Unlock-Bedingungen
- einfache Assets oder Textressourcen

Warum dieser Slice:

- deckt Eingabe, Ressource, Progression und Softlock-Validierung ab,
- vermeidet Computer-/USB-/Assembly-Komplexität am Anfang,
- prüft trotzdem den wichtigsten End-to-End-Pfad.

Danach iterieren:

1. Computer-Login und Computer-Oberfläche.
2. Choice/E-Mail.
3. USB-Item-Use.
4. Control Panel.
5. Assembly/Bildfragmente.
6. erweiterte Hint-Unlock-Bedingungen.

## Baustein-Support

Die UI darf langfristig die komplette V0-Bausteinpalette darstellen. Für den
produktiven Paket-Flow muss aber zwischen "sichtbar im Konzept" und
"Generator-unterstützt" unterschieden werden.

V0-Regel:

```text
Ein Entwurf darf nur als generator-faehiges Paket erstellt werden, wenn alle
verwendeten Bausteine im aktuellen Generator-Slice unterstuetzt sind.
```

Noch nicht generator-fähige Bausteine dürfen im UI-Prototyp sichtbar sein,
müssen aber deaktiviert oder klar markiert werden. Die UI muss den Grund nennen,
z. B. "noch nicht im Generator unterstützt" oder "benötigt Computer-Surface,
die im aktuellen Slice noch nicht verfügbar ist". Sonst entsteht für Lehrende
der falsche Eindruck, dass jeder sichtbare Baustein bereits spielbar erzeugt
werden kann.

## UI-Aufgaben

Die UI muss nicht visuell fest vorgegeben werden. Layout, Komponenten,
Navigation und Interaktionsdesign bleiben frei.

Funktional muss die UI:

- Wizard-Schritte aus `teacher-workflow-v0.md` abbilden,
- fachliche Eingaben erfassen,
- technische Daten automatisch ableiten,
- stabile IDs erzeugen,
- `deer.json` intern erstellen,
- Assets annehmen und referenzieren,
- blockierende Fehler vor `deer.zip erstellen` anzeigen,
- Warnungen anzeigen, aber nicht blockieren,
- `deer.zip` für Teilen und manuellen Generatorlauf erstellen,
- Speicherort und nächste manuelle Aktion anzeigen.

UI darf frei entscheiden:

- Liste, Timeline, Board oder Canvas für den Ablauf,
- Drag-and-drop oder Button-basierte Bedienung,
- genaue Formulierungen,
- Schritt-Navigation,
- visuelle Statusanzeigen.

UI darf nicht:

- ungültige `deer.json` an den Generator schicken,
- technische Tokens als Lehrenden-Hauptbegriff zeigen,
- optionale Progressionspfade erzeugen,
- den Paket-erstellen-Button trotz blockierender Fehler aktivieren,
- eine Preview oder einen Neu-Generieren-Flow als V0-Pflicht einplanen.

## Generator-Aufgaben

Der Generator konsumiert:

- interne `deer.json`,
- referenzierte Assets,
- ggf. feste Generator-Konfiguration aus Code oder separater Konfigurationsdatei.

Der Generator erzeugt:

- Runtime-Dateien,
- ein fertiges Room-Paket auf Disk,
- strukturierten Validierungs-/Fehlerbericht.

Der Generator validiert erneut:

- Schema-Version,
- Pflichtfelder,
- ID- und Referenzintegrität,
- Asset-Existenz,
- Baustein-Parameter,
- Oberflächen-Kompatibilität,
- Graph-Erreichbarkeit,
- keine Softlocks,
- keine ungewollten Skips,
- Hint-Unlock-Referenzen.

Generator-Laufparameter wie Seed oder Layout-Profil gehören nicht in
`deer.json`. Falls später gebraucht, kommen sie in eine separate
Generator-Konfiguration oder einen CLI/API-Parameter.

## Generator-Aufruf

V0-Entscheidung:

- Der Wizard startet den Java-Generator noch nicht automatisch.
- Der Wizard erzeugt `deer.zip` als teilbares Authoring-/Content-Paket.
- Der Generator wird für V0 manuell mit diesem Paket oder dem entpackten
  Projektordner aufgerufen.
- Ein lokaler Backend-Service, offizieller CLI-Wrapper oder Desktop-Start ist
  eine nächste Iteration.

Vorschlag für die nächste Iteration:

```text
generate
  --input <projectRoot-or-deer.zip>
  --output <outputDir>
  --format room_zip
  --report <outputDir>/validation/report.json
```

Stabile Exit-Codes:

| Code | Bedeutung |
|---:|---|
| 0 | erfolgreich generiert |
| 1 | Validierung fehlgeschlagen |
| 2 | Generatorfehler |
| 3 | Umgebung/Konfiguration fehlt oder ist ungültig |

Erfolgsantwort:

```json
{
  "success": true,
  "artifact": {
    "type": "room_zip",
    "path": "D:/.../generated/room.zip"
  },
  "reportPath": "D:/.../generated/validation/report.json",
  "issues": []
}
```

Fehlerantwort:

```json
{
  "success": false,
  "artifact": null,
  "reportPath": "D:/.../generated/validation/report.json",
  "issues": []
}
```

## Validierungsmodell

UI und Generator sollten dieselben Fehlerklassen verwenden.

Gemeinsames Issue-Format:

```json
{
  "severity": "error",
  "phase": "graph",
  "code": "GRAPH_UNREACHABLE_RIDDLE",
  "message": "Das Rätsel ist vom Start aus nicht erreichbar.",
  "path": "/riddleGraph/nodes/4",
  "entity": {
    "kind": "riddle",
    "id": "r_storage_keypad"
  },
  "relatedPaths": ["/riddles/5/requiresTokens"],
  "blocking": true
}
```

Regeln:

- `path` ist ein JSON Pointer.
- `code` ist stabil und maschinenlesbar.
- `message` ist nutzerlesbar.
- `entity` erleichtert UI-Markierung.
- Warnungen haben `severity=warning` und `blocking=false`.

Blockierend:

- Pflichtfeld fehlt,
- unbekannte ID-Referenz,
- required Asset fehlt,
- Assettyp nicht unterstützt,
- unbekannter Bausteintyp,
- fehlender Pflichtparameter für Baustein,
- inkompatible Baustein-/Oberflächen-Kombination,
- `surfaceId` zeigt auf keine existierende Surface,
- unerreichbares Progressionsrätsel,
- Progressionsrätsel kann übersprungen werden,
- Endzustand nicht erreichbar,
- zyklische oder unlösbare Abhängigkeit,
- Hint-Unlock verweist auf nicht existierendes Ziel.

Warnung:

- lange Texte,
- Rätsel ohne Hinweise,
- ungenutzte Assets,
- schwache Story-Einbettung,
- geschätzte Dauer passt schlecht zum Zeitlimit.

## Packaging-Entscheidung

V0 braucht ein teilbares Paket nach der Wizard-Validierung.

Pragmatischer Startpunkt:

- Erstes Wizard-Artefakt: `deer.zip`.
- `deer.zip` enthält `deer.json` und Assets und kann geteilt oder vom
  Generator manuell konsumiert werden.
- Der Generator-Output muss so gekapselt sein, dass später ohne Schema-Bruch
  ein startbarer Ordner, eine runnable `.jar` oder eine `.exe` als weiterer
  Packaging-Target ergänzt werden kann.
- Für Lehrende ist später eine One-Click-Lösung besser, aber sie ist nicht
  Voraussetzung für den ersten V0-Schnitt.

Entscheidung für Implementierungsstart:

```text
V0 startet mit deer.zip als erstem Wizard-Paket. One-Click-Packaging und
automatischer Generator-Aufruf sind spätere Iterationen und dürfen den
deer.json-Contract nicht verändern.
```

## Entscheidungsfragen An Product/Team

Diese Fragen sind die Punkte, die ich noch gezielt klären würde. Meine
Default-Antwort steht jeweils dabei. Wenn niemand widerspricht, würde ich diese
Defaults als V0-Arbeitsannahme verwenden.

### Muss Vor Dem Implementierungsstart Geklärt Sein

1. Wie startet die Web-App den Java-Generator?
   - V0-Entscheidung: gar nicht automatisch. Die UI erstellt `deer.zip`; der
     Generator wird manuell gestartet. CLI-/Backend-Wrapper folgt später.

2. Was ist das erste Paket-Artefakt?
   - V0-Entscheidung: `deer.zip`. Später kann daraus ein startbarer Ordner,
     `.jar` oder `.exe` werden.

3. Welche Bausteine müssen im ersten End-to-End-Slice wirklich spielbar sein?
   - Empfehlung: `collection.single`, `input.numeric`, einfache Türaktion.

4. Welche Bausteine dürfen in der UI sichtbar sein, obwohl der Generator sie
   noch nicht voll unterstützt?
   - Default: Im UI-Konzept dürfen alle V0-Bausteine sichtbar sein. Der
     Paket-erstellen-Button darf aber nur für generator-fähige Bausteine aktiv
     werden. Noch nicht unterstützte Bausteine brauchen Feature-Flags oder eine
     klare Markierung mit Begründung.

5. Wie wird ein Wizard-Entwurf gespeichert und wieder geöffnet?
   - Default: lokaler Projektordner mit `deer.json` und Asset-Unterordner.
     Lehrende bearbeiten nicht direkt JSON, aber die App kann den Entwurf wieder
     laden.

6. Wie sieht das Generator-Fehlerformat aus?
   - Default: strukturierte Fehler mit `severity`, `code`, `message`, `path`
     und optional `riddleId`, `assetId`, `nodeId`.

7. Was ist `successEffect`?
   - Default: ein interner, kontrollierter Effekt nach erfolgreichem Rätsel,
     nicht UI-Freitext. Die UI zeigt fachlich "Tür öffnen"; intern steht z. B.
     `{ "type": "open_surface", "surfaceId": "s_storage_door" }`.

8. Unterstützt V0 mehrere Computer/Keypads oder startet der Generator mit je
   einer Oberfläche pro Typ?
   - Default: Datenmodell und UI dürfen mehrere Oberflächen ausdrücken. Der
     erste Generator-Slice darf je Typ mit einer Oberfläche starten, solange
     die UI das nicht als voll unterstützt verkauft.

### Kann Während V0 Iteriert Werden

9. Wie streng soll das JSON Schema typ-spezifische `parameters` prüfen?
   - Default: Schema prüft Grundstruktur und einfache Typen. Generator und UI
     prüfen zunächst die tieferen Bausteinregeln. Nach dem ersten Slice kann
     das Schema pro Baustein verschärft werden.

10. Was bedeutet `time.limitMode=soft` im ersten Generator?
    - Default: In V0 speichern und validieren, Runtime-Effekt aber minimal
      halten. Stärkere Hinweise nach Ablauf können später folgen.

11. Wie werden Hint-Unlock-Events technisch gemappt?
    - Default: Die UI bietet fachliche Bedingungen an. Der Generator mappt sie
      auf Petri-Net-Places/Tokens oder Runtime-Events.

12. Wie streng soll die UI Story-Qualität bewerten?
    - Default: Nur Warnungen, keine Blocker.

13. Wann gilt ein Progressionsrätsel als überspringbar?
    - Empfehlung: Wenn ein späterer erforderlicher Zustand erreichbar ist,
      ohne dass dieses Rätsel gelöst wurde.

### Kann Sicher Nach V0 Warten

Diese Punkte müssen nicht gelöst sein, bevor UI und Generator starten:

- One-Click `.exe` oder Installer,
- automatischer UI-Aufruf des Java-Generators,
- offizieller CLI-/Backend-Wrapper,
- spielbare Preview,
- expliziter Neu-Generieren-Flow,
- mehrere Themes,
- Custom-Themes/Tilesets/Sprites/UI-Skins,
- Lernziel-, Evaluation-, Debriefing- oder Telemetrie-Funktionen,
- frei editierbarer technischer Graph für Lehrende.

## Startkriterium Für Umsetzung

UI und Generator können starten, wenn:

- `deer.schema.json` als V0-Contract akzeptiert ist,
- `surfaces`/`surfaceId` als internes Oberflächenmodell akzeptiert sind,
- erster End-to-End-Slice festgelegt ist,
- klar ist, welche Bausteine im ersten Slice wirklich generierbar sind,
- erstes Output-Format entschieden ist,
- Validierungsfehlerformat grob entschieden ist,
- die erste kontrollierte `successEffect`-Struktur festgelegt ist.
