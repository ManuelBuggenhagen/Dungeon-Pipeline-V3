# Wizard Documentation

Status: V0-Konzept, UI-Contract und Quellen liegen jetzt unter `wizard/doc`.

## Struktur

- `v0/`: aktuelle V0-Arbeitsgrundlage für Wizard-UI, `deer.json`,
  Validierung, Packaging und Generator-Übergabe.
- `research/`: wissenschaftliche Quellen und Literaturhinweise. Diese Dateien
  sind Kontext, aber kein UI-Pflichtumfang für V0.

## Wichtig Für Die UI-Umsetzung

Die UI-Person sollte mit diesen Dateien starten:

1. `v0/implementation-handoff-v0.md`: kompakte Produkt- und
   Implementierungsübergabe.
2. `v0/wizard-ui-flow-v0.md`: sichtbare Wizard-Schritte, Eingaben und
   Validierungsstellen.
3. `v0/teacher-workflow-v0.md`: funktionaler Lehrenden-Workflow ohne
   UI-Layout-Vorgabe.
4. `v0/deer.schema.json`: maschinenlesbarer Contract für die erzeugte
   `deer.json`.
5. `v0/examples/deer.example.json`: gültiges Beispiel für den erwarteten
   Export.
6. `v0/parameter-table-v0.md`: Pflichtparameter und optionale Felder je
   Baustein.

Nützlicher Kontext, aber nicht zwingend für den ersten UI-Slice:

- `v0/concept.md`: kurzer Scope und Produktgedanke.
- `v0/the-last-hour-interaction-catalog.md`: Herkunft der verfügbaren
  Bausteine aus The Last Hour.
- `v0/room-package-format.md`: `deer.zip`, späteres Runtime-Paket und Starter.
- `research/sources/ordered/`: wissenschaftliche Quellen.
