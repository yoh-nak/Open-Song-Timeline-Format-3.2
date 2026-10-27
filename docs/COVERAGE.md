# OSTF 3.2.0 — 全項目・サンプル対応表 / Complete Coverage Index

**基準 / Authority:** [`schema/ostf-3.2.0.schema.json`](../schema/ostf-3.2.0.schema.json)。これは旧3.1の集計ではなく、3.2の宣言を再カウントした静的索引です。

| Metric | Count |
|---|---:|
| Named Schema definitions / 名前付き定義 | 64 |
| Named property declarations / プロパティ宣言 | 242 |
| Actual finite data-field declarations / 実フィールド宣言 | 229 |
| Field declarations with `.ostf` witnesses / 実例あり | 229 |
| Selector-only `if` predicate declarations / 条件式中の比較宣言 | 13 |
| Standalone top-level `.ostf` samples | 77 |
| Legacy OSTF 3.0 `.ostf` samples | 3 |
| Total `.ostf` sample documents | 80 |

The 13 selector-only declarations are schema conditions, not separate serialized source properties. Unknown vendor-specific extension payload keys and unbounded BCP 47 language tags cannot be enumerated by any finite corpus.

## 完全な229実フィールド宣言と実例 / All 229 field declarations

| # | 3.2 Schema declaration | `.ostf` example | JSON Pointer in example |
|---:|---|---|---|
| 1 | `#/properties/format` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/format` |
| 2 | `#/properties/version` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/version` |
| 3 | `#/properties/ppq` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/ppq` |
| 4 | `#/properties/song` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/song` |
| 5 | `#/properties/timeline` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline` |
| 6 | `#/properties/harmony` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony` |
| 7 | `#/properties/tracks` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks` |
| 8 | `#/properties/soundSlots` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/soundSlots` |
| 9 | `#/properties/lyrics` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics` |
| 10 | `#/properties/resources` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/resources` |
| 11 | `#/properties/extensions` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions` |
| 12 | `#/properties/drumKits` | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) | `/drumKits` |
| 13 | `#/properties/contributors` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/contributors` |
| 14 | `#/$defs/ref/properties/type` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/ref/type` |
| 15 | `#/$defs/ref/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/ref/id` |
| 16 | `#/$defs/range/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/targetRange/start` |
| 17 | `#/$defs/range/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/targetRange/duration` |
| 18 | `#/$defs/spelledPitchClass/properties/step` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/root/step` |
| 19 | `#/$defs/spelledPitchClass/properties/alter` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/root/alter` |
| 20 | `#/$defs/song/properties/title` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/song/title` |
| 21 | `#/$defs/song/properties/primaryMelodyTrackId` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/song/primaryMelodyTrackId` |
| 22 | `#/$defs/song/properties/localizedTitles` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/localizedTitles` |
| 23 | `#/$defs/song/properties/subtitle` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/subtitle` |
| 24 | `#/$defs/song/properties/description` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/description` |
| 25 | `#/$defs/song/properties/alternateTitles` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/alternateTitles` |
| 26 | `#/$defs/song/properties/credits` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/song/credits` |
| 27 | `#/$defs/section/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/sections/0/id` |
| 28 | `#/$defs/section/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/sections/0/name` |
| 29 | `#/$defs/section/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/sections/0/start` |
| 30 | `#/$defs/section/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/sections/0/duration` |
| 31 | `#/$defs/section/properties/role` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/sections/0/role` |
| 32 | `#/$defs/section/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/timeline/sections/0/localizedNames` |
| 33 | `#/$defs/tempo/properties/at` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/tempoChanges/0/at` |
| 34 | `#/$defs/tempo/properties/bpm` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/tempoChanges/0/bpm` |
| 35 | `#/$defs/meter/properties/at` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/meterChanges/0/at` |
| 36 | `#/$defs/meter/properties/numerator` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/meterChanges/0/numerator` |
| 37 | `#/$defs/meter/properties/denominator` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/meterChanges/0/denominator` |
| 38 | `#/$defs/timeline/properties/sections` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/sections` |
| 39 | `#/$defs/timeline/properties/tempoChanges` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/tempoChanges` |
| 40 | `#/$defs/timeline/properties/meterChanges` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/timeline/meterChanges` |
| 41 | `#/$defs/chord/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/id` |
| 42 | `#/$defs/chord/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/start` |
| 43 | `#/$defs/chord/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/duration` |
| 44 | `#/$defs/chord/properties/label` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/label` |
| 45 | `#/$defs/chord/properties/root` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/root` |
| 46 | `#/$defs/chord/properties/intervals` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords/0/intervals` |
| 47 | `#/$defs/chord/properties/bass` | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) | `/harmony/chords/0/bass` |
| 48 | `#/$defs/chord/properties/voicing` | [`03_harmony_only.ostf`](../samples/03_harmony_only.ostf) | `/harmony/chords/0/voicing` |
| 49 | `#/$defs/scale/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales/0/id` |
| 50 | `#/$defs/scale/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales/0/start` |
| 51 | `#/$defs/scale/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales/0/duration` |
| 52 | `#/$defs/scale/properties/label` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales/0/label` |
| 53 | `#/$defs/scale/properties/tonic` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales/0/tonic` |
| 54 | `#/$defs/scale/properties/intervals` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales/0/intervals` |
| 55 | `#/$defs/harmony/properties/chords` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/chords` |
| 56 | `#/$defs/harmony/properties/scales` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/harmony/scales` |
| 57 | `#/$defs/instrument/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/instrument/name` |
| 58 | `#/$defs/instrument/properties/family` | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) | `/tracks/0/instrument/family` |
| 59 | `#/$defs/instrument/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/tracks/0/instrument/localizedNames` |
| 60 | `#/$defs/note/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/notes/0/id` |
| 61 | `#/$defs/note/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/notes/0/start` |
| 62 | `#/$defs/note/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/notes/0/duration` |
| 63 | `#/$defs/note/properties/pitch` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/notes/0/pitch` |
| 64 | `#/$defs/note/properties/soundSlotId` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/3/clips/0/notes/0/soundSlotId` |
| 65 | `#/$defs/note/properties/velocity` | [`04_melody_only.ostf`](../samples/04_melody_only.ostf) | `/tracks/0/clips/0/notes/0/velocity` |
| 66 | `#/$defs/clip/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/id` |
| 67 | `#/$defs/clip/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/name` |
| 68 | `#/$defs/clip/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/start` |
| 69 | `#/$defs/clip/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/duration` |
| 70 | `#/$defs/clip/properties/enabled` | [`10_enabled_states.ostf`](../samples/10_enabled_states.ostf) | `/tracks/0/clips/1/enabled` |
| 71 | `#/$defs/clip/properties/notes` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips/0/notes` |
| 72 | `#/$defs/clip/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/tracks/0/clips/0/localizedNames` |
| 73 | `#/$defs/track/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/id` |
| 74 | `#/$defs/track/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/name` |
| 75 | `#/$defs/track/properties/role` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/role` |
| 76 | `#/$defs/track/properties/instrument` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/instrument` |
| 77 | `#/$defs/track/properties/enabled` | [`10_enabled_states.ostf`](../samples/10_enabled_states.ostf) | `/tracks/1/enabled` |
| 78 | `#/$defs/track/properties/clips` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/tracks/0/clips` |
| 79 | `#/$defs/track/properties/trackType` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/tracks/0/trackType` |
| 80 | `#/$defs/track/properties/preferredView` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/tracks/0/preferredView` |
| 81 | `#/$defs/track/properties/soundSource` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/tracks/0/soundSource` |
| 82 | `#/$defs/track/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/tracks/0/localizedNames` |
| 83 | `#/$defs/track/properties/drumKitId` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/tracks/0/drumKitId` |
| 84 | `#/$defs/soundSlot/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/soundSlots/0/id` |
| 85 | `#/$defs/soundSlot/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/soundSlots/0/name` |
| 86 | `#/$defs/soundSlot/properties/role` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/soundSlots/0/role` |
| 87 | `#/$defs/soundSlot/properties/midiNote` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/soundSlots/0/midiNote` |
| 88 | `#/$defs/soundSlot/properties/midiMapping` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/soundSlots/0/midiMapping` |
| 89 | `#/$defs/soundSlot/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/soundSlots/0/localizedNames` |
| 90 | `#/$defs/lyricDocument/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/documents/0/id` |
| 91 | `#/$defs/lyricDocument/properties/title` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/documents/0/title` |
| 92 | `#/$defs/lyricDocument/properties/language` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/documents/0/language` |
| 93 | `#/$defs/lyricPhrase/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases/0/id` |
| 94 | `#/$defs/lyricPhrase/properties/documentId` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases/0/documentId` |
| 95 | `#/$defs/lyricPhrase/properties/text` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases/0/text` |
| 96 | `#/$defs/lyricPhrase/properties/pronunciation` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases/0/pronunciation` |
| 97 | `#/$defs/lyricPhrase/properties/start` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases/0/start` |
| 98 | `#/$defs/lyricPhrase/properties/duration` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases/0/duration` |
| 99 | `#/$defs/lyricUnit/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/units/0/id` |
| 100 | `#/$defs/lyricUnit/properties/phraseId` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/units/0/phraseId` |
| 101 | `#/$defs/lyricUnit/properties/text` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/units/0/text` |
| 102 | `#/$defs/lyricUnit/properties/pronunciation` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/units/0/pronunciation` |
| 103 | `#/$defs/lyricUnit/properties/start` | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) | `/lyrics/units/0/start` |
| 104 | `#/$defs/lyricUnit/properties/duration` | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) | `/lyrics/units/0/duration` |
| 105 | `#/$defs/lyricAlignment/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/alignments/0/id` |
| 106 | `#/$defs/lyricAlignment/properties/unitIds` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/alignments/0/unitIds` |
| 107 | `#/$defs/lyricAlignment/properties/noteIds` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/alignments/0/noteIds` |
| 108 | `#/$defs/lyrics/properties/documents` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/documents` |
| 109 | `#/$defs/lyrics/properties/phrases` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/phrases` |
| 110 | `#/$defs/lyrics/properties/units` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/units` |
| 111 | `#/$defs/lyrics/properties/alignments` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/lyrics/alignments` |
| 112 | `#/$defs/resource/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/resources/0/id` |
| 113 | `#/$defs/resource/properties/kind` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/resources/0/kind` |
| 114 | `#/$defs/resource/properties/uri` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/resources/0/uri` |
| 115 | `#/$defs/resource/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/resources/0/name` |
| 116 | `#/$defs/resource/properties/sha256` | [`16_generation_context.ostf`](../samples/16_generation_context.ostf) | `/resources/0/sha256` |
| 117 | `#/$defs/dependencies/oneOf/0/properties/scope` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/dependencies/scope` |
| 118 | `#/$defs/dependencies/oneOf/1/properties/scope` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/dependencies/scope` |
| 119 | `#/$defs/dependencies/oneOf/1/properties/refs` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/com.example.generator-state.v1/dependencies/refs` |
| 120 | `#/$defs/extension/properties/version` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/version` |
| 121 | `#/$defs/extension/properties/status` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/status` |
| 122 | `#/$defs/extension/properties/dependencies` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/dependencies` |
| 123 | `#/$defs/extension/properties/data` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data` |
| 124 | `#/$defs/relativeAssignment/properties/noteId` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/noteId` |
| 125 | `#/$defs/relativeAssignment/properties/basis` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/basis` |
| 126 | `#/$defs/relativeAssignment/properties/contextId` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/contextId` |
| 127 | `#/$defs/relativeAssignment/properties/anchorPitch` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/anchorPitch` |
| 128 | `#/$defs/relativeAssignment/properties/index` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/index` |
| 129 | `#/$defs/relativeAssignment/properties/chromaticOffset` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/chromaticOffset` |
| 130 | `#/$defs/relativeAssignment/allOf/0/then/properties/index` | [`13_relative_scale.ostf`](../samples/13_relative_scale.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/index` |
| 131 | `#/$defs/relativeData/properties/assignments` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data/assignments` |
| 132 | `#/$defs/generator/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/generator/id` |
| 133 | `#/$defs/generator/properties/version` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/generator/version` |
| 134 | `#/$defs/generationInput/properties/role` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/role` |
| 135 | `#/$defs/generationInput/properties/ref` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/ref` |
| 136 | `#/$defs/generationInput/properties/readRange` | [`16_generation_context.ostf`](../samples/16_generation_context.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/1/readRange` |
| 137 | `#/$defs/generationProcess/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/id` |
| 138 | `#/$defs/generationProcess/properties/generator` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/generator` |
| 139 | `#/$defs/generationProcess/properties/inputs` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs` |
| 140 | `#/$defs/generationProcess/properties/outputs` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/outputs` |
| 141 | `#/$defs/generationProcess/properties/targetRange` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/targetRange` |
| 142 | `#/$defs/generationProcess/properties/protectedNoteIds` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/protectedNoteIds` |
| 143 | `#/$defs/generationProcess/properties/vendorExtension` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes/0/vendorExtension` |
| 144 | `#/$defs/generationData/properties/processes` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data/processes` |
| 145 | `#/$defs/roleDefinition/properties/id` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/roleDefinitions/0/id` |
| 146 | `#/$defs/roleDefinition/properties/name` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/roleDefinitions/0/name` |
| 147 | `#/$defs/roleDefinition/properties/baseRole` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/roleDefinitions/0/baseRole` |
| 148 | `#/$defs/objectColor/properties/target` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/appearance/objectColors/0/target` |
| 149 | `#/$defs/objectColor/properties/color` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/appearance/objectColors/0/color` |
| 150 | `#/$defs/appearanceData/properties/roleDefinitions` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/roleDefinitions` |
| 151 | `#/$defs/appearanceData/properties/sectionRoleAssignments` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/sectionRoleAssignments` |
| 152 | `#/$defs/appearanceData/properties/appearance` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/appearance` |
| 153 | `#/$defs/appearanceData/properties/appearance/properties/roleColors` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/appearance/roleColors` |
| 154 | `#/$defs/appearanceData/properties/appearance/properties/customRoleColors` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/appearance/customRoleColors` |
| 155 | `#/$defs/appearanceData/properties/appearance/properties/objectColors` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data/appearance/objectColors` |
| 156 | `#/$defs/legacyData/properties/sourceVersion` | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) | `/extensions/org.ostf.legacy-import.v1/data/sourceVersion` |
| 157 | `#/$defs/legacyData/properties/sourceSha256` | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) | `/extensions/org.ostf.legacy-import.v1/data/sourceSha256` |
| 158 | `#/$defs/legacyData/properties/sourceText` | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) | `/extensions/org.ostf.legacy-import.v1/data/sourceText` |
| 159 | `#/$defs/legacyData/properties/conversionReport` | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) | `/extensions/org.ostf.legacy-import.v1/data/conversionReport` |
| 160 | `#/$defs/extensions/properties/org.ostf.relative-phrase.v1` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1` |
| 161 | `#/$defs/extensions/properties/org.ostf.generation-context.v1` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1` |
| 162 | `#/$defs/extensions/properties/org.ostf.appearance.v1` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1` |
| 163 | `#/$defs/extensions/properties/org.ostf.legacy-import.v1` | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) | `/extensions/org.ostf.legacy-import.v1` |
| 164 | `#/$defs/extensions/properties/org.ostf.phrase-library.v1` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1` |
| 165 | `#/$defs/extensions/properties/org.ostf.clip-choices.v1` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1` |
| 166 | `#/$defs/extensions/properties/org.ostf.localization.v1` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1` |
| 167 | `#/$defs/extensions/properties/org.ostf.relative-phrase.v1/allOf/1/then/properties/data` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.relative-phrase.v1/data` |
| 168 | `#/$defs/extensions/properties/org.ostf.generation-context.v1/allOf/1/then/properties/data` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.generation-context.v1/data` |
| 169 | `#/$defs/extensions/properties/org.ostf.appearance.v1/allOf/1/then/properties/data` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) | `/extensions/org.ostf.appearance.v1/data` |
| 170 | `#/$defs/extensions/properties/org.ostf.legacy-import.v1/allOf/1/then/properties/data` | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) | `/extensions/org.ostf.legacy-import.v1/data` |
| 171 | `#/$defs/extensions/properties/org.ostf.phrase-library.v1/allOf/1/then/properties/data` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data` |
| 172 | `#/$defs/extensions/properties/org.ostf.clip-choices.v1/allOf/1/then/properties/data` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data` |
| 173 | `#/$defs/extensions/properties/org.ostf.localization.v1/allOf/1/then/properties/data` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data` |
| 174 | `#/$defs/phraseTemplate/properties/id` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/id` |
| 175 | `#/$defs/phraseTemplate/properties/duration` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/duration` |
| 176 | `#/$defs/phraseTemplate/properties/notes` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes` |
| 177 | `#/$defs/phraseTemplate/properties/name` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/name` |
| 178 | `#/$defs/phraseNoteMapping/properties/templateNoteId` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap/0/templateNoteId` |
| 179 | `#/$defs/phraseNoteMapping/properties/repeatIndex` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap/0/repeatIndex` |
| 180 | `#/$defs/phraseNoteMapping/properties/noteId` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap/0/noteId` |
| 181 | `#/$defs/phraseInstance/properties/id` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/id` |
| 182 | `#/$defs/phraseInstance/properties/templateId` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/templateId` |
| 183 | `#/$defs/phraseInstance/properties/clipId` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/clipId` |
| 184 | `#/$defs/phraseInstance/properties/repeatCount` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/repeatCount` |
| 185 | `#/$defs/phraseInstance/properties/transpose` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/transpose` |
| 186 | `#/$defs/phraseInstance/properties/noteMap` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap` |
| 187 | `#/$defs/phraseLibraryData/properties/templates` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates` |
| 188 | `#/$defs/phraseLibraryData/properties/instances` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/instances` |
| 189 | `#/$defs/clipCandidate/properties/id` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates/0/id` |
| 190 | `#/$defs/clipCandidate/properties/clipIds` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates/0/clipIds` |
| 191 | `#/$defs/clipCandidate/properties/name` | [`55_choice_empty_candidate.ostf`](../samples/55_choice_empty_candidate.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates/0/name` |
| 192 | `#/$defs/clipChoiceGroup/properties/id` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/id` |
| 193 | `#/$defs/clipChoiceGroup/properties/candidates` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates` |
| 194 | `#/$defs/clipChoiceGroup/properties/selectedCandidateId` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/selectedCandidateId` |
| 195 | `#/$defs/clipChoiceGroup/properties/name` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups/0/name` |
| 196 | `#/$defs/clipChoicesData/properties/groups` | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) | `/extensions/org.ostf.clip-choices.v1/data/groups` |
| 197 | `#/$defs/midiMapping/properties/standard` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/soundSlots/0/midiMapping/standard` |
| 198 | `#/$defs/midiMapping/properties/note` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/soundSlots/0/midiMapping/note` |
| 199 | `#/$defs/midiMapping/allOf/0/then/properties/note` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/soundSlots/0/midiMapping/note` |
| 200 | `#/$defs/soundSource/properties/kind` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) | `/tracks/0/soundSource/kind` |
| 201 | `#/$defs/soundSource/properties/identifier` | [`64_custom_drum_mapping.ostf`](../samples/64_custom_drum_mapping.ostf) | `/tracks/0/soundSource/identifier` |
| 202 | `#/$defs/drumKit/properties/id` | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) | `/drumKits/0/id` |
| 203 | `#/$defs/drumKit/properties/name` | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) | `/drumKits/0/name` |
| 204 | `#/$defs/drumKit/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/drumKits/0/localizedNames` |
| 205 | `#/$defs/drumKit/properties/soundSlotIds` | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) | `/drumKits/0/soundSlotIds` |
| 206 | `#/$defs/phraseSourceNote/properties/id` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/id` |
| 207 | `#/$defs/phraseSourceNote/properties/start` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/start` |
| 208 | `#/$defs/phraseSourceNote/properties/duration` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/duration` |
| 209 | `#/$defs/phraseSourceNote/properties/pitch` | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/pitch` |
| 210 | `#/$defs/phraseSourceNote/properties/soundSlotId` | [`23_phrase_percussion.ostf`](../samples/23_phrase_percussion.ostf) | `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/soundSlotId` |
| 211 | `#/$defs/voicing/properties/pitches` | [`03_harmony_only.ostf`](../samples/03_harmony_only.ostf) | `/harmony/chords/0/voicing/pitches` |
| 212 | `#/$defs/voicing/properties/velocities` | [`03_harmony_only.ostf`](../samples/03_harmony_only.ostf) | `/harmony/chords/0/voicing/velocities` |
| 213 | `#/$defs/contributor/properties/id` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/contributors/0/id` |
| 214 | `#/$defs/contributor/properties/name` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/contributors/0/name` |
| 215 | `#/$defs/contributor/properties/kind` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/contributors/0/kind` |
| 216 | `#/$defs/contributor/properties/localizedNames` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/contributors/0/localizedNames` |
| 217 | `#/$defs/credit/properties/id` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/song/credits/0/id` |
| 218 | `#/$defs/credit/properties/contributorId` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/song/credits/0/contributorId` |
| 219 | `#/$defs/credit/properties/role` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/song/credits/0/role` |
| 220 | `#/$defs/credit/properties/label` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/credits/1/label` |
| 221 | `#/$defs/alternateTitle/properties/id` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/alternateTitles/0/id` |
| 222 | `#/$defs/alternateTitle/properties/title` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | `/song/alternateTitles/0/title` |
| 223 | `#/$defs/localizationTarget/properties/kind` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries/0/target/kind` |
| 224 | `#/$defs/localizationTarget/properties/id` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries/0/target/id` |
| 225 | `#/$defs/localizationTarget/properties/profile` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries/0/target/profile` |
| 226 | `#/$defs/localizationEntry/properties/target` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries/0/target` |
| 227 | `#/$defs/localizationEntry/properties/field` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries/0/field` |
| 228 | `#/$defs/localizationEntry/properties/values` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries/0/values` |
| 229 | `#/$defs/localizationData/properties/entries` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) | `/extensions/org.ostf.localization.v1/data/entries` |

## Conditions (13 predicate-only declarations)

| # | Schema pointer |
|---:|---|
| 1 | `#/$defs/extensions/properties/org.ostf.appearance.v1/allOf/1/if/properties/version` |
| 2 | `#/$defs/extensions/properties/org.ostf.clip-choices.v1/allOf/1/if/properties/version` |
| 3 | `#/$defs/extensions/properties/org.ostf.generation-context.v1/allOf/1/if/properties/version` |
| 4 | `#/$defs/extensions/properties/org.ostf.legacy-import.v1/allOf/1/if/properties/version` |
| 5 | `#/$defs/extensions/properties/org.ostf.localization.v1/allOf/1/if/properties/version` |
| 6 | `#/$defs/extensions/properties/org.ostf.phrase-library.v1/allOf/1/if/properties/version` |
| 7 | `#/$defs/extensions/properties/org.ostf.relative-phrase.v1/allOf/1/if/properties/version` |
| 8 | `#/$defs/localizationTarget/allOf/0/if/properties/kind` |
| 9 | `#/$defs/localizationTarget/allOf/1/if/properties/kind` |
| 10 | `#/$defs/midiMapping/allOf/0/if/properties/standard` |
| 11 | `#/$defs/relativeAssignment/allOf/0/if/properties/basis` |
| 12 | `#/$defs/soundSource/allOf/0/if/properties/kind` |
| 13 | `#/$defs/track/allOf/0/if/properties/trackType` |

## All finite enum values / 全列挙値

Values for source fields, as distinct from a duplicate selector constraint in `if` expressions.

| Declaration | Allowed value | Source witness |
|---|---|---|
| `#/$defs/ref/properties/type` | `section` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `track` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `clip` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/ref/properties/type` | `note` | [`17_appearance.ostf`](../samples/17_appearance.ostf) |
| `#/$defs/ref/properties/type` | `chord` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/ref/properties/type` | `scale` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `soundSlot` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `drumKit` | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) |
| `#/$defs/ref/properties/type` | `lyricDocument` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `lyricPhrase` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `lyricUnit` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `lyricAlignment` | [`38_dependency_scopes.ostf`](../samples/38_dependency_scopes.ostf) |
| `#/$defs/ref/properties/type` | `resource` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `A` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `B` | [`35_pitch_class_spellings.ostf`](../samples/35_pitch_class_spellings.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `C` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `D` | [`13_relative_scale.ostf`](../samples/13_relative_scale.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `E` | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `F` | [`35_pitch_class_spellings.ostf`](../samples/35_pitch_class_spellings.ostf) |
| `#/$defs/spelledPitchClass/properties/step` | `G` | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) |
| `#/$defs/meter/properties/denominator` | `1` | [`36_all_meter_denominators.ostf`](../samples/36_all_meter_denominators.ostf) |
| `#/$defs/meter/properties/denominator` | `2` | [`36_all_meter_denominators.ostf`](../samples/36_all_meter_denominators.ostf) |
| `#/$defs/meter/properties/denominator` | `4` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/meter/properties/denominator` | `8` | [`11_meter_tempo_gaps.ostf`](../samples/11_meter_tempo_gaps.ostf) |
| `#/$defs/meter/properties/denominator` | `16` | [`36_all_meter_denominators.ostf`](../samples/36_all_meter_denominators.ostf) |
| `#/$defs/meter/properties/denominator` | `32` | [`36_all_meter_denominators.ostf`](../samples/36_all_meter_denominators.ostf) |
| `#/$defs/meter/properties/denominator` | `64` | [`36_all_meter_denominators.ostf`](../samples/36_all_meter_denominators.ostf) |
| `#/$defs/track/properties/trackType` | `melodic` | [`62_melodic_piano_roll.ostf`](../samples/62_melodic_piano_roll.ostf) |
| `#/$defs/track/properties/trackType` | `drum` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) |
| `#/$defs/track/properties/trackType` | `mixed` | [`63_mixed_track.ostf`](../samples/63_mixed_track.ostf) |
| `#/$defs/track/properties/preferredView` | `auto` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) |
| `#/$defs/track/properties/preferredView` | `pianoRoll` | [`62_melodic_piano_roll.ostf`](../samples/62_melodic_piano_roll.ostf) |
| `#/$defs/track/properties/preferredView` | `drumGrid` | [`64_custom_drum_mapping.ostf`](../samples/64_custom_drum_mapping.ostf) |
| `#/$defs/resource/properties/kind` | `audio` | [`16_generation_context.ostf`](../samples/16_generation_context.ostf) |
| `#/$defs/resource/properties/kind` | `midi` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/resource/properties/kind` | `text` | [`16_generation_context.ostf`](../samples/16_generation_context.ostf) |
| `#/$defs/resource/properties/kind` | `other` | [`37_resource_kinds.ostf`](../samples/37_resource_kinds.ostf) |
| `#/$defs/dependencies/oneOf/0/properties/scope` | `document` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/dependencies/oneOf/0/properties/scope` | `none` | [`18_opaque_precision.ostf`](../samples/18_opaque_precision.ostf) |
| `#/$defs/extension/properties/status` | `current` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/extension/properties/status` | `stale` | [`14_stale_source.ostf`](../samples/14_stale_source.ostf) |
| `#/$defs/extension/properties/status` | `unverified` | [`15_unverified_source.ostf`](../samples/15_unverified_source.ostf) |
| `#/$defs/relativeAssignment/properties/basis` | `chordTone` | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) |
| `#/$defs/relativeAssignment/properties/basis` | `scaleDegree` | [`13_relative_scale.ostf`](../samples/13_relative_scale.ostf) |
| `#/$defs/midiMapping/properties/standard` | `gm1` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) |
| `#/$defs/midiMapping/properties/standard` | `custom` | [`64_custom_drum_mapping.ostf`](../samples/64_custom_drum_mapping.ostf) |
| `#/$defs/soundSource/properties/kind` | `unassigned` | [`68_empty_declared_drum_track.ostf`](../samples/68_empty_declared_drum_track.ostf) |
| `#/$defs/soundSource/properties/kind` | `gm1` | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) |
| `#/$defs/soundSource/properties/kind` | `externalMidi` | [`70_external_and_host_sources.ostf`](../samples/70_external_and_host_sources.ostf) |
| `#/$defs/soundSource/properties/kind` | `plugin` | [`64_custom_drum_mapping.ostf`](../samples/64_custom_drum_mapping.ostf) |
| `#/$defs/soundSource/properties/kind` | `hostManaged` | [`70_external_and_host_sources.ostf`](../samples/70_external_and_host_sources.ostf) |
| `#/$defs/contributor/properties/kind` | `person` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/contributor/properties/kind` | `organization` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/contributor/properties/kind` | `other` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `song` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `alternateTitle` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `contributor` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `credit` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `section` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `track` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `clip` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `soundSlot` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `drumKit` | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `lyricDocument` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `resource` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `phraseTemplate` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `clipCandidate` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `clipChoiceGroup` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/kind` | `roleDefinition` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/profile` | `org.ostf.phrase-library.v1` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/profile` | `org.ostf.clip-choices.v1` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |
| `#/$defs/localizationTarget/properties/profile` | `org.ostf.appearance.v1` | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) |

**Enumerated source-field values:** 72. **Without sample:** 0.

## Examples and major capability areas

| Files | Feature |
|---|---|
| 01–17 | Song, timeline, harmony, melodic and percussion notes, meter and optional context profiles |
| 18–34 | Precision and unknown profiles, lyrics, phrase templates and choice groups |
| 35–59 | Complete enumeration and boundary examples for inherited fields and optional profiles |
| 60–70 | Arrangement, GM1/custom drums, Sound Slots, Mixed tracks, renderer hints and collision boundaries |
| 71–73 | Multiple Drum Kits, unrestricted-language metadata, contributors and unused Sound Slots |
| 74–76 | Optional note Velocity in melody, percussion and phrase instances |
| 77 | 3.2 chord voicing pitch/Velocity versus performed primary melody Velocity |
| legacy_3_0/* | Unchanged 3.0 original samples for non-destructive compatibility comparison |

## Limitations

- Every `.ostf` document is a complete UTF-8 JSON example. A subset is deliberately from 3.0 or 3.1 to demonstrate the version boundary; do not relabel an old document on read.
- The presence of a JSON field is structural coverage, not proof of cross-document semantic validity, DAW interchange, MIDI export or audio playback.
- Where an extension is opaque or parameter text is open-ended, no finite set of samples can exercise every possible value.
- 3.2 `voicing.velocities` must have the same number of entries as `voicing.pitches` even though JSON Schema alone cannot generally enforce sibling lengths.

