# Wizard Workspace

Status: V0-Konzept und UI-first Authoring-Contract

## Aktive Dateien

- `concept.md`: kurze aktuelle Projektdefinition für V0.
- `wizard-ui-flow-v0.md`: erwarteter Wizard-Ablauf und sichtbare Eingaben.
- `teacher-workflow-v0.md`: funktionaler UI-Contract für den Lehrenden-
  Workflow, inklusive Eingaben und Validierungszeitpunkten.
- `deer-json-spec.md`: menschenlesbare Spezifikation der internen
  `deer.json`, die der Generator konsumiert.
- `deer.schema.json`: maschinenlesbares JSON Schema für `deer.json`.
- `examples/deer.example.json`: valides Beispiel mit den aktuell verfügbaren
  V0-Bausteinen.
- `parameter-table-v0.md`: Pflichtparameter der V0-Bausteine.
- `the-last-hour-interaction-catalog.md`: Mapping vorhandener The-Last-Hour-
  Interaktionen auf wiederverwendbare Wizard-Bausteine.
- `room-package-format.md`: Packaging-/Generator-Notiz für `deer.zip` und
  spätere Runtime-Pakete.
- `implementation-handoff-v0.md`: kompakte Übergabe für UI- und
  Generator-Start.

## V0-Entscheidungen

- Die sichtbare Endaktion in V0 ist ein validiertes `deer.zip` zu erstellen,
  nicht nur `deer.json` zu exportieren.
- Der Java-Generator wird in V0 manuell mit diesem Paket oder Projektordner
  gestartet. Automatischer UI-Aufruf, Backend oder offizieller CLI-Wrapper
  folgen später.
- Live-Preview und Neu-Generieren-Button sind nicht Teil von V0.
- V0 startet mit `deer.zip` als erstem teilbaren Paket. Startbarer Ordner,
  `.jar`, `.exe` oder Installer sind spätere Packaging-Iterationen.
- V0 nutzt genau ein Standard-Theme.
- V0 fragt keine Lernziele, Evaluation, Debriefing, Telemetrie oder
  Pre-/Post-Tests ab.
- The Last Hour ist nur Quelle für vorhandene Spielbausteine und Assets, keine
  vorausgewählte Vorlage.
- Die `deer.json` enthält keine Generator-Laufparameter wie Seed,
  Layout-Profil oder technische Constraints.
- Oberflächen entstehen aus den gewählten Bausteinen. Lehrende sollen keine
  technische Slot-Struktur vorab planen müssen.
- Alle aktuell aus The Last Hour ableitbaren Bausteine dürfen im UI-Konzept
  angeboten werden. Paket-Erstellung darf aber nur für Bausteine aktiv werden,
  die der Generator im jeweiligen Slice wirklich unterstützt. Deaktivierte
  Bausteine müssen den Grund sichtbar machen.
- Blockierend sind nur Fehler, die zu Softlocks, nicht erreichbaren Rätseln,
  unspielbaren Progressionen oder unerlaubten Skips führen.
