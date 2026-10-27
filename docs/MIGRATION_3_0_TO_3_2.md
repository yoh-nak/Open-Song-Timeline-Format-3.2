# OSTF 3.0 / 3.1 → OSTF 3.2 Migration / 旧版との互換・移行

This guide preserves the inherited 3.0 → 3.1 migration rules below and supplements them with 3.2 rules. The 3.2 wire version is `3.2.0`; the 3.0/3.1 files need not be rewritten merely to be read. All seven supported data functions introduced in 3.2 are optional. The 3.1 Schema is distributed unchanged.

**3.1 → 3.2:** Upon an *explicit* user-requested migration, update the root `version` to `3.2.0`, preserve all 3.1 objects, IDs, tick offsets, notes and omitted Velocity exactly; do not fabricate credits, source languages, chord voicings or translations. 3.1 `localizedNames` and `localizedTitles` remain valid and must not be removed. All six frozen optional profiles stay at their original `1.0.0-draft.1` versions.

**3.2 → 3.1:** Present an accurate loss inventory and obtain user permission before removing 3.2-only `harmony.chords[].voicing`, root `contributors`, `song.credits`, `song.subtitle`, `song.description`, `song.alternateTitles`, and `org.ostf.localization.v1`. Without permission, refuse lossy downgrade and leave the original 3.2 file unchanged. Do not substitute voicing pitches for actual performance notes or silently create tracks. When conversion is requested, preserve the original source file separately.

**3.2 → 3.0:** Apply the 3.2→3.1 rules *and* the 3.1→3.0 loss warnings for localized names, drum kits and performed-note Velocity. Old readers cannot be assumed to accept a `3.2.0` root version. Do not disguise new wire data by only changing the version label.

**1.3 → 3.2:** Although both versions may have a `voicing.pitches` concept, the overall document objects and wire versions differ. A real semantic migration is required; changing `version` alone is invalid.

**Lexical integrity:** `1e400`, `1e-400` and long decimal tokens in opaque extensions must not be converted through binary floating point, rounded, rewritten as `Infinity` or flattened to `0.0`. The corrected [opaque precision sample](../samples/18_opaque_precision.ostf) is authoritative for this 3.2 package; the published 3.1 sample contained invalid `Infinity` and is not reproduced here.

**Reference preservation:** Unknown extension versions/data should remain opaque. An editor removing a referenced source object must not silently re-point `current` localization entries. Translating sung lyric text is not a metadata replacement; lyric alignment must remain independent.

## Previous 3.0 → 3.1 documentation (historical)

# OSTF 3.0.0 ↔ 3.1.0 — Conservative migration

**New 3.1 source of truth:** [Specification](FORMAT_SPECIFICATION.en.md#17-ostf-310-track-semantics-editor-hints-and-portable-midi-routing-normative), [3.1 schema](../schema/ostf-3.1.0.schema.json). The 3.0 schema remains bundled and unchanged.

## 3.0 → 3.1 (non-destructive)

- Parse and validate the document as 3.0.0, preserving opaque numeric values and exact JSON token classes. No migration execution has been carried out for the release.
- Change root `version` to `3.1.0` **only by explicit export/conversion**.
- All 3.0.0 time values, notes, slots, chords, lyrics, extensions, and IDs stay unchanged. 3.0 notes have no `velocity`, and the converter MUST NOT invent one.
- `trackType` is OPTIONAL; when absent the reader infers it from `pitch` and `soundSlotId` across all stored notes. An optional `--infer-track-types` action may permanently add an unambiguous inferred value on nonempty tracks. Empty tracks stay unspecified unless the author chooses one.
- `preferredView` defaults semantically to auto; do not invent a saved view.
- **Do not invent translations, language tags, drum kits or kit IDs from existing names or MIDI channel numbers.** Existing UTF-8 track/slot/clip/section names survive unchanged. Root `drumKits[]` is optional and absent by default.
- Do **not** infer a GM1 mapping from a `midiNote` hint, a note number such as 36, a slot name, track.role or plugin.
- Do **not** turn pitch notes into percussion slots automatically, select a new instrument, delete disabled data or generate clips.
- The six provisional extension profiles retain exact versions; preserve unknown envelopes.

Example: `python ostf31_migrate.py samples/legacy_3_0/06_percussion_slots.ostf new-drum.ostf --infer-track-types`.

## 3.1 → 3.0 (potentially lossy)

The 3.0 schema forbids all new 3.1 keys, including performed-note `velocity`. The downgrade drops `trackType`, `preferredView`, `soundSource`, `midiMapping`, all `localizedNames` / `localizedTitles`, all `drumKitId` references and the root `drumKits[]` (even if explicitly empty) ONLY after `--allow-loss` and reports every removed JSON path. The original `name` and `song.title` values remain unchanged. The legacy `midiNote` is retained if already present; an explicit `midiMapping` is **not** silently recast into a `midiNote` because mapping certainty would become a mere hint. All note fields **other than optional `velocity`** remain unchanged. Source explicit velocity values are dropped ONLY after `--allow-loss` and every removed field path is reported; `coreNotesChanged` is then true. A reader supporting only 3.0 will not directly read 3.1.

Example: `python ostf31_migrate.py samples/61_gm1_drum_grid.ostf older.ostf --to 3.0.0 --allow-loss`.

## Failure and integrity

Input format and semantic errors abort the conversion. Input/output aliases are refused; existing output requires explicit `--force`. A temporary file is staged before a destination replacement. These are source-level design constraints; **no CLI execution or DAW round-trip test was performed for this package**. Changes to metadata can alter display behavior in readers. Only explicit approved 3.1→3.0 velocity loss removes a Core performed-note attribute; onset, gate, IDs and targets remain unchanged.


## Compact GitHub distribution / 100ファイル制限

The 100-file official distribution preserves stable 3.0 schemas and three verbatim reference files but does **not** bundle earlier standalone converters (`ostf31_migrate.py`) or the 2.3 archive. Migration semantics above remain authoritative; users must implement or obtain an adapter separately and must never treat a bare `version` edit as an executed migration.

正式版では仕様と実例の網羅を優先し、旧CLI移行プログラムと2.3原典は同梱しません。変換の損失報告・原本保持の義務は維持します。
