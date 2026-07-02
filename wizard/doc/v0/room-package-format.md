# Room Package Format

## Current Decisions

- The editable authoring source is `deer.json`.
- The visible V0 action for educators is downloading a validated `deer.zip`.
- `deer.zip` is the first shareable Wizard package.
- Importing an existing `deer.zip` back into the Wizard is not part of V0.
- In V0, the Java generator is started manually with `deer.zip` or the
  unpacked project folder.
- Dungeon needs a new starter that can load a generated Wizard package or the
  runtime package derived from it.
- Backend, official CLI wrapper, runnable `.jar`, `.exe`, or installer are
  later iterations.
- One-click packaging remains a later iteration.
- The first version supports only LibGDX-friendly custom media.
- The first version uses one standard theme.
- The wizard does not generate evaluation artifacts, telemetry profiles,
  debriefing guides, or pre/post tests in v0.
- Generated runtime files are derived from `deer.json` by the manually started
  generator and can be overwritten by the generator.
- Custom assets are content assets, not theme replacements.
- The educator workflow should be simple: fill the wizard, validate the design,
  download/share a `deer.zip`.
- The current web wizard target is: create authoring data, validate it, package
  `deer.json` and assets into `deer.zip`.
- Live preview is out of V0.
- A dedicated regenerate button is out of V0.
- Deterministic regeneration with an explicit seed can be supported later as a
  generator option, not as part of `deer.json`.

## Packaging Direction

The first implementation creates `deer.zip`. The goal after V0 is still a
one-click or near-one-click result for non-technical educators, but that should
not block the first foundation slice.

Candidate formats:

| Format | Benefit | Cost / Risk |
|---|---|---|
| `deer.zip` authoring/content package | Small, inspectable, shareable, can be consumed manually by the generator. | Not one-click; requires a separate generator/start step. |
| Generated folder with launcher script | Practical V0 path; can include `deer.json`, assets, runtime files and `start.bat`. | Multiple files; sharing is less clean unless zipped. |
| Self-contained `.jar` | One file, close to existing Java/Gradle packaging patterns. | Requires Java or a bundled runtime; larger artifact. |
| Windows `.exe` launcher | Best one-click Windows UX. | Platform-specific, bigger build/signing/distribution burden. |
| Installer or app bundle | Cleanest end-user install story. | Probably too much scope for V0. |

V0 recommendation: start with `deer.zip` as the Wizard authoring/content package
format. Keep the structure compatible with later generation of a startable
folder, runnable `.jar`, Windows `.exe`, or installer. A Windows `.exe` should
only be evaluated after the generator contract is stable.

For V0, "package created" means: the Wizard successfully produces a valid
`deer.zip` that contains the authoring model and assets. It does not yet mean
that the educator receives a polished one-click executable.

## Authoring And Generation Workflow

The current UI-first workflow is:

```text
Web wizard
  -> writes/updates deer.json
  -> validates game-breaking constraints
  -> writes referenced assets into the project folder
  -> creates deer.zip
  -> user downloads deer.zip
  -> Java generator is started manually in V0
```

There is no live preview step and no dedicated regenerate button in V0.

Internal generator flow:

```text
deer.zip or project folder
  -> manual Java generator run
  -> runtime files
  -> selected packaging format
```

The educator should not have to manually edit or copy a JSON file. `deer.json`
is the internal source inside the package; `deer.zip` is the shareable handoff.
Importing an existing `deer.zip` into the Wizard can be added later, but is out
of V0.

If deterministic regeneration is needed later, the seed should be provided to
the generator separately, for example through a CLI parameter, UI field in the
generator screen, or a separate run file. It should not be stored in
`deer.json`, because `deer.json` describes the intended room, not a specific
generator execution.

Preview can be revisited later, but is intentionally outside V0.

V0 Wizard package:

```text
deer.zip
  deer.json
  assets/
    custom/
  validation/
    authoring-report.json
```

Later generated runtime package:

```text
generated-room/
  deer.json
  room.json
  README.md
  levels/
    main_1.level
  riddles/
    graph.json
  assets/
    custom/
  validation/
    generator-report.json
```

Dungeon-side starter requirement:

```text
Wizard package or generated-room
  -> new Dungeon starter
  -> load selected generated room
```

The first starter can be pragmatic. It only needs to prove that a generated room
package can be loaded without requiring educators or students to manually place
files in engine internals.

## File Roles

### `deer.json`

The only editable source file. The wizard reads and writes this file.

It contains the authoring model:

- room metadata,
- selected predefined theme,
- target audience,
- player count and time limit,
- story beats,
- riddle graph,
- riddle parameters,
- custom content asset references.

The current detailed draft is stored in
[`deer-json-spec.md`](deer-json-spec.md). The first machine-readable schema is
stored in [`deer.schema.json`](deer.schema.json). A small valid V0
example is stored in [`examples/deer.example.json`](examples/deer.example.json).

### `deer.zip`

The first shareable V0 package.

It contains:

- `deer.json`,
- uploaded or copied assets with relative paths,
- authoring validation report,
- optional README or package metadata later.

It does not have to contain generated `.level` files in the first Wizard
version. Those are produced by the manually started generator.

### `room.json`

Generated runtime manifest.

It contains:

- package format version,
- room id,
- start level,
- selected theme id,
- required systems,
- player setup,
- asset mappings,
- riddle graph entrypoint,
- validation artifact entrypoints.

### `levels/main_1.level`

Generated Dungeon level file. For v0, one package contains one playable level.
The naming follows the existing Dungeon level variant convention.

### `riddles/graph.json`

Generated runtime riddle graph. This should remain readable even if the first
runtime implementation later compiles it into Petri-net structures.

### `assets/custom/`

Custom educator uploads. These are limited to LibGDX-friendly content media in
v0, such as images and audio files supported by the runtime.

Simple text content should be entered directly in the wizard and stored in
`deer.json`, not uploaded as arbitrary documents.

Not supported in v0:

- custom tilesets,
- custom player sprites,
- custom enemy sprites,
- custom UI skins,
- shader/theme replacements,
- arbitrary documents that LibGDX cannot load directly.

If an educator needs document-like content, the first version should prefer text
entered into the wizard or image assets generated/exported outside the wizard.

### `validation/`

Validation support. This is not learning evaluation. It only records whether the
package is structurally and technically usable.

`authoring-report.json` records Wizard-side schema, graph, asset, and parameter
checks. A later generator report can record runtime/generator checks.

The client should prevent invalid states before the educator can create a
package. Generator validation is still required as a safety net for application
bugs, edited JSON files, and future import workflows.

The package button should be disabled until the client preflight is valid.
Blocking errors should be shown at the step or graph element that caused them.
Warnings may remain visible at package time, but must not be confused with
blocking errors.

## Evaluation, Debriefing, Telemetry, Pre/Post Tests

The wizard should not generate evaluation artifacts, debriefing guides,
telemetry profiles, or pre/post tests in v0.

This can be revisited later, but it is intentionally outside the first final
version. Plausible later options are:

- educator-authored questions inside the wizard,
- predefined learning environments with predefined question banks,
- external question sets imported as content.
