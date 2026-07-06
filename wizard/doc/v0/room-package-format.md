# Room Package Format

Status: V0-Contract
Scope: Export aus dem Web-Wizard

## V0-Entscheidungen

- Der Web-Wizard exportiert ein validiertes `deer.zip`.
- `deer.zip` ist das einzige V0-Exportpaket des Wizards.
- Das Paket enthält `deer.json` und alle in `deer.json` referenzierten Assets.
- Das Einlesen bestehender `deer.zip`-Pakete in den Wizard ist nicht Teil von
  V0.
- Der Java-Generator wird in V0 manuell mit `deer.zip` oder dem entpackten
  Paketordner gestartet.
- Runtime-Dateien werden erst vom manuell gestarteten Generator abgeleitet und
  gehören nicht in das Wizard-Exportpaket.
- Custom Assets ergänzen Inhalte, ersetzen aber nicht das Standard-Theme.
- V0 unterstützt nur Medien, die die LibGDX-Runtime direkt verarbeiten kann.

## V0-Paketstruktur

```text
deer.zip
  deer.json
  assets/
    custom/
```

Regeln:

- `deer.json` liegt im Wurzelverzeichnis des Pakets.
- Asset-Pfade in `deer.json` sind relativ zum Paket.
- Referenzierte Assets müssen im Paket existieren.
- Pfade dürfen nicht aus dem Paket herauszeigen.
- Nicht referenzierte Dateien sind für V0 nicht erforderlich.

## File Roles

### `deer.json`

Die einzige editierbare Authoring-Quelle. Der Wizard liest und schreibt diese
Datei und exportiert sie im Paket.

Sie enthält:

- Raum-Metadaten,
- Session-Parameter,
- Standard-Theme und Storytexte,
- Oberflächen,
- Rätselgraph,
- Rätselparameter,
- Asset-Referenzen.

Die detaillierte Spezifikation steht in
[`deer-json-spec.md`](deer-json-spec.md). Das maschinenlesbare Schema steht in
[`deer.schema.json`](deer.schema.json). Ein gültiges Beispiel steht in
[`examples/deer.example.json`](examples/deer.example.json).

### `assets/custom/`

Custom Assets sind von Lehrenden hochgeladene oder vom Wizard ausgewählte
Inhaltsmedien. In V0 sind das nur Runtime-fähige Dateien wie Bilder, Audio und
einfache Textdateien.

Nicht Teil von V0:

- Custom Tilesets,
- Custom Player Sprites,
- Custom Enemy Sprites,
- Custom UI-Skins,
- Theme-Ersetzungen,
- beliebige Dokumentformate, die die Runtime nicht direkt laden kann.

Textinhalte sollen bevorzugt im Wizard eingegeben und in `deer.json`
gespeichert werden. Wenn ein Inhalt als Datei gebraucht wird, muss er als
unterstütztes Asset im Paket liegen.

## Validierung Und Handoff

Der Wizard darf `deer.zip` nur erzeugen, wenn Schema, Graph,
Pflichtparameter und Asset-Referenzen valide sind. Blockierende Fehler werden
vor dem Export im Client angezeigt.

Der manuell gestartete Java-Generator validiert dieselben harten Regeln erneut,
bevor er Runtime-Dateien ableitet. Diese zweite Prüfung ersetzt nicht die
Client-Validierung; sie schützt vor manuell veränderten Paketinhalten und
Implementierungsfehlern.

Für V0 endet die Wizard-Verantwortung mit dem validierten `deer.zip`. Alles,
was danach aus dem Paket erzeugt wird, gehört zum Generator- und Runtime-Scope.
