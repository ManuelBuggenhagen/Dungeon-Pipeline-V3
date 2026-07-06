# Wizard V0 Documentation

Status: V0-Konzept und UI-first Authoring-Contract.

## Zweck

Diese Dateien beschreiben den öffentlichen V0-Stand des Dungeon Wizards. V0 ist
eine Web-App für nicht-technische Lehrende und erzeugt ein validiertes
`deer.zip` mit `deer.json` und Assets.

## Aktive Dateien

Diese V0-Dokumente liegen unter `./wizard/doc/v0`. Der Root `./wizard` bleibt
frei für Web-App-Code, Paket-Output und spätere Generator-Anbindung.

- `concept.md`: aktuelle Projektdefinition und Scope-Grenze für V0.
- `frontend-handoff-overview-v0.md`: kompakter Einstieg für die
  Frontend-/UI-Person.
- `wizard-ui-flow-v0.md`: Wizard-Schritte, sichtbare Eingaben,
  Validierungsstellen und Export.
- `teacher-workflow-v0.md`: funktionaler UI-Contract für den Lehrenden-
  Workflow.
- `deer-json-spec.md`: menschenlesbare Spezifikation der internen
  `deer.json`, die der Generator konsumiert.
- `deer.schema.json`: maschinenlesbares JSON Schema für `deer.json`.
- `examples/deer.example.json`: valides Beispiel mit den aktuell verfügbaren
  V0-Bausteinen.
- `parameter-table-v0.md`: Pflichtparameter der V0-Bausteine.
- `the-last-hour-interaction-catalog.md`: Mapping vorhandener The-Last-Hour-
  Interaktionen auf wiederverwendbare Wizard-Bausteine.
- `room-package-format.md`: Packaging- und Generator-Notiz für `deer.zip`.
- `implementation-handoff-v0.md`: kompakte Übergabe für UI- und
  Generator-Start.

## V0-Kernvertrag

- Die sichtbare Endaktion ist `deer.zip herunterladen`.
- Das Paket enthält die validierte `deer.json` und alle referenzierten Assets.
- Lehrende bearbeiten keine JSON-Datei direkt.
- Der Java-Generator wird in V0 manuell mit dem Paket oder Projektordner
  gestartet.
- The Last Hour liefert verfügbare Bausteine und Assets, aber keine
  vorausgewählte Vorlage.
- Paket-Erstellung ist nur aktiv, wenn Client-Preflight, Schema,
  Asset-Referenzen, Ablauf und verwendete Bausteine gültig sind.
- Nicht generierbare Bausteine oder Optionen erscheinen deaktiviert mit
  sichtbarem Grund.
- Blockierend sind Fehler, die Pflichtdaten, Assets, Progression oder
  Spielbarkeit beschädigen. Warnungen blockieren nicht.
