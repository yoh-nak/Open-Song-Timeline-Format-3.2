# Open Song Timeline Format — OSTF 3.2.0

**Proposed official GitHub specification distribution · 100 files maximum · UTF-8 JSON `.ostf`**

OSTF is a lightweight, DAW-independent interchange format for sharing **song structure, chords, a designated main melody, lyrics, and optional simple arrangement notes** between composition tools and plugins. It is **not a DAW session or plugin instrument/preset format**.

OSTF 3.2 retains the complete OSTF 3.1 music interchange model while adding two bounded improvements: an **optional chord-voicing snapshot with per-pitch optional Velocity**, and **uniform multilingual names, song metadata and song-local contributor credits**. These never require extra synthesizers, additional melody tracks, audio rendering, MIDI controllers or a localization UI.

## Documentation / 仕様書

| Resource | English | 日本語 |
|---|---|---|
| Normative 3.2 specification | [Specification](docs/FORMAT_SPECIFICATION.en.md) | [正式仕様](docs/FORMAT_SPECIFICATION.ja.md) |
| Full manual | [Complete manual](docs/COMPLETE_MANUAL.en.md) | [全網羅マニュアル](docs/COMPLETE_MANUAL.ja.md) |
| Full parameter reference | [All parameters](docs/PARAMETER_REFERENCE.en.md) | [全パラメーター](docs/PARAMETER_REFERENCE.ja.md) |
| Field-to-example index | [Coverage](docs/COVERAGE.md) | [Samples](samples/INDEX.md) |
| Compatibility and migration | [3.0/3.1→3.2](docs/MIGRATION_3_0_TO_3_2.md) | [同左](docs/MIGRATION_3_0_TO_3_2.md) |
| Schema | [OSTF 3.2 JSON Schema](schema/ostf-3.2.0.schema.json) | [Preserved 3.1 Schema](schema/ostf-3.1.0.schema.json) |

## Core 3.2 rules

- `harmony.chords[].voicing.pitches`: optional fixed chord pitch snapshot. Exact MIDI pitches 0–127, duplicates and source order preserved.
- `harmony.chords[].voicing.velocities`: optional array of 1–127 integers or `null`, positionally **same length** as `pitches`. Chord voicing is metadata for display/preview, **not a new played track**. No implicit MIDI generation, voicing preview or DAW export is required.
- `tracks[].clips[].notes[].velocity`: already present in 3.1; still optional for main melody and all other performed notes. A missing value stays missing, not automatically 100.
- Optional `contributors[]`, ordered `song.credits[]`, `song.subtitle`, `song.description` and stable-ID `song.alternateTitles[]`.
- One optional `org.ostf.localization.v1` extension using **unbounded number of BCP 47 language-tag entries (subject to BCP 47 validity)**, with stable target IDs and a prescribed list of translatable text fields. Original Unicode source text remains. 3.1 `localizedNames`/`localizedTitles` are still accepted without destructive conversion.
- No primary-melody duplication: `song.primaryMelodyTrackId` still names at most one main melody track; backing vocals/harmonies may use normal arrangement tracks.
- No standard plugin presets, Bank/Program Change, CC, Pitch Bend, Aftertouch, mixer state, lyric translation alignment or sound synthesis.

## Start with the samples

| Example | What it demonstrates |
|---|---|
| [77_primary_melody_and_chord_voicing_velocity.ostf](samples/77_primary_melody_and_chord_voicing_velocity.ostf) | Melody Note Velocity vs chord voicing Velocity in the same song |
| [03_harmony_only.ostf](samples/03_harmony_only.ostf) | Chord display voicing without implied performed notes |
| [71_multilingual_tracks_and_multiple_drum_kits.ostf](samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | Credits, contributors, translated titles and arrangements |
| [58_complete_workflow.ostf](samples/58_complete_workflow.ostf) | Optional profiles and localized names for profile-local objects |
| [18_opaque_precision.ostf](samples/18_opaque_precision.ostf) | Fixed JSON numeric token preservation sample |

There are **77 top-level `.ostf` samples** (including one intentionally original `3.1.0` sample) plus **3 unchanged OSTF 3.0 originals**. Read [sample index](samples/INDEX.md) and [coverage](docs/COVERAGE.md) for the full corpus. All text is UTF-8; languages and song-local contributor role tokens are not a fixed locale menu.

## Compatibility and scope

Readers implementing 3.2 may read a 3.0/3.1 file while preserving its original root version; an older 3.1 implementation is not required to accept new 3.2 files. Downgrading a 3.2 document is an **explicit lossy operation** requiring a loss report and approval. Unsupported extension namespaces and data must not be silently dropped on round trip if preservation is claimed. Sound source and DAW routing remain application-specific.

**Release decision:** GO for publishing the versioned specification/schema/manuals/samples as a documentation package after static consistency checks. **No DAW, Max for Live, MIDI export, sound engine, independent consumer or interoperability test is claimed.** Repository publishing/committing is not performed by this package. Suggested release tag: `v3.2.0`. See [release notes](RELEASE_NOTES.md) and [license](LICENSE.md).

## 3.2 percussion playback and portable mapping clarification

Each Sound Slot ID is an independent, document-local percussion target even when names, roles or MIDI keys match. Concurrent snare/clap hits, repeated hits and multiple percussion tracks retain their event identities. A player can render these with built-in approximate sounds without creating DAW MIDI clips, but sound choices are **local to the player**, not encoded as sample or preset state. An explicit `gm1` mapping may drive approximate GM previews; `custom` key 36 does not mean GM kick. One-shot audio tails and choke groups remain renderer-specific. MIDI converters must disclose slot-identity loss and unresolved destination maps. See [normative sections 18.6–18.7](docs/FORMAT_SPECIFICATION.en.md), [complete manual](docs/COMPLETE_MANUAL.en.md), [example 61](samples/61_gm1_drum_grid.ostf), and [example 64](samples/64_custom_drum_mapping.ostf). No new wire fields, schema-acceptance changes or sample-file count changes are involved.

## Final 3.2 interoperability clarifications (no new fields)

Normative [Sections 18.8–18.9](docs/FORMAT_SPECIFICATION.en.md) clarify a previously under-specified boundary: one Mixed track can contain pitched key 36 and a GM1 kick mapped to 36 **simultaneously**, without the two OSTF events becoming one. An adapter separates routing when necessary and reports collision/loss when not possible. Two overlapping same-pitch notes also remain two OSTF notes even when MIDI1 Note Off behavior becomes ambiguous at the destination. Player-local per-pad mute and preview tones are not new saved Core parameters; `track.enabled` and `clip.enabled` remain the exchangeable mute controls. Source document-local IDs are never global instrument preset IDs. [Sample 63](samples/63_mixed_track.ostf) and [sample 69](samples/69_muted_and_overlapping.ostf) show the edge cases. The schema and 100-file count are unchanged.

## Implementation note: 3.2 display and reference reader

Chord voicing pitches/Velocities are *non-performing annotations*; only adopted track Note events are automatically realized. Song and Track views should use the same main-melody Note IDs, never synthesize duplicate chord-voicing MIDI on load. The bundled `ostf3.py` is a read/validate/event-extract library, **not** a MIDI writer or a localization UI. Its `choose_display_name` covers object-local 3.1 fields, not 3.2 document-wide localization entries. See [specification 18.10](docs/FORMAT_SPECIFICATION.en.md) and the [manual implementation correction](docs/COMPLETE_MANUAL.en.md).

## Verified document inventory for the 100-file distribution

The index was recomputed from the actual **3.2 JSON Schema**: **64 named definitions, 242 named property declarations** (229 observable source fields and 13 selector-only condition declarations). Every finite observable field has at least one complete standalone `.ostf` source witness. The inherited 3.1 figure of 194 is not the 3.2 total. See the [full 3.2 schema-to-example index](docs/COVERAGE.md).

Unzip into the **repository root**. If the GitHub web UI imposes a lower upload batch limit, use more than one commit/upload batch or Git CLI. An already published tag must not be silently overwritten.
