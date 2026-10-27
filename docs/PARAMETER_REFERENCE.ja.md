# OSTF 3.2.0 — 全網羅パラメーターリファレンス

**正本：** [`schema/ostf-3.2.0.schema.json`](../schema/ostf-3.2.0.schema.json) と [正式仕様書](FORMAT_SPECIFICATION.ja.md)。旧3.1資料ではなく3.2 Schemaから全定義を再列挙しています。

**範囲：** ルート13フィールド、名前付き定義64件、名前付きプロパティ宣言242件（実データフィールド229件＋条件判定用の宣言13件）。実フィールド229件すべてに `.ostf` の記述例があります。任意の拡張 `extensions.*.data` や自由文字列、BCP 47言語の可能な組み合わせは有限個には列挙できません。

「必須」は**その親オブジェクト内での必須指定**です。該当親自体が任意なら、その項目が全ファイルで必須になるわけではありません。条件付き要求・参照整合性・並列配列の長さはSchema単独では完結しない場合があります。

### 音楽的な意味の境界

- `harmony.chords[].voicing.pitches` はコードの情報上の音高（0〜127）、`velocities` は同じインデックスに対応する任意の強弱（1〜127または `null`）。実演奏ノートの自動生成や二重再生を意味しません。
- `tracks[].clips[].notes[].velocity` はメインメロディーを含む**実演奏ノート**の任意属性。未指定は欠落として保存します。
- `soundSlotId` はMIDIノート番号ではなく文書内での打楽器識別子。`midiMapping.standard` を確認し、`custom`をGM1と同一視しません。
- `localization` は任意の表示翻訳。元の名前・クレジットIDを置換しません。歌詞の発音割当や訳詞は独立です。
- 動作検証や別ソフトとの交換が済んだことを意味する資料ではありません。

## ルートフィールド

| 名称 | 型 | 必須 | 制約 | `.ostf`実例 |
|---|---|---|---|---|
| `format` | `const` | Yes | const="OSTF" | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/format` |
| `version` | `const` | Yes | const="3.2.0" | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/version` |
| `ppq` | `integer` | Yes | minimum=1; maximum=960000 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/ppq` |
| `song` | `song` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/song` |
| `timeline` | `timeline` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline` |
| `harmony` | `harmony` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony` |
| `tracks` | `array` | Yes | items=#/$defs/track | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks` |
| `soundSlots` | `array` | Yes | items=#/$defs/soundSlot | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/soundSlots` |
| `lyrics` | `lyrics` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics` |
| `resources` | `array` | Yes | items=#/$defs/resource | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/resources` |
| `extensions` | `extensions` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions` |
| `drumKits` | `array` | — | items=#/$defs/drumKit | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) `/drumKits` |
| `contributors` | `array` | — | items=#/$defs/contributor | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/contributors` |

## 名前付き定義：全64件

右端はパラメーターが実際に登場する完全な `.ostf` サンプルへの参照です。

### `id`

- **Schema:** `#/$defs/id`
- **Type:** `string`
- **追加制約:** `not`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `pattern="^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$"`

### `nonnegative`

- **Schema:** `#/$defs/nonnegative`
- **Type:** `integer`

- **スカラー・分岐制約:** `minimum=0; maximum=9007199254740991`

### `positive`

- **Schema:** `#/$defs/positive`
- **Type:** `integer`

- **スカラー・分岐制約:** `minimum=1; maximum=9007199254740991`

### `signed`

- **Schema:** `#/$defs/signed`
- **Type:** `integer`

- **スカラー・分岐制約:** `minimum=-9007199254740991; maximum=9007199254740991`

### `pitch`

- **Schema:** `#/$defs/pitch`
- **Type:** `integer`

- **スカラー・分岐制約:** `minimum=0; maximum=127`

### `ref`

- **Schema:** `#/$defs/ref`
- **Type:** `object`
- **必須フィールド:** `type`, `id`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `type` | `enum` | Yes | enum=[section, track, clip, note, chord, scale, soundSlot, drumKit, lyricDocument, lyricPhrase, lyricUnit, lyricAlignment, resource] | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/ref/type` |
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/ref/id` |

### `range`

- **Schema:** `#/$defs/range`
- **Type:** `object`
- **必須フィールド:** `start`, `duration`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `start` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/targetRange/start` |
| `duration` | `positive` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/targetRange/duration` |

### `spelledPitchClass`

- **Schema:** `#/$defs/spelledPitchClass`
- **Type:** `object`
- **必須フィールド:** `step`, `alter`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `step` | `enum` | Yes | enum=[A, B, C, D, E, F, G] | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/root/step` |
| `alter` | `integer` | Yes | minimum=-2; maximum=2 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/root/alter` |

### `song`

- **Schema:** `#/$defs/song`
- **Type:** `object`
- **必須フィールド:** `title`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `title` | `string` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/song/title` |
| `primaryMelodyTrackId` | `id` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/song/primaryMelodyTrackId` |
| `localizedTitles` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/localizedTitles` |
| `subtitle` | `string` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/subtitle` |
| `description` | `string` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/description` |
| `alternateTitles` | `array` | — | items=#/$defs/alternateTitle | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/alternateTitles` |
| `credits` | `array` | — | items=#/$defs/credit | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/song/credits` |

### `section`

- **Schema:** `#/$defs/section`
- **Type:** `object`
- **必須フィールド:** `id`, `name`, `start`, `duration`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/sections/0/id` |
| `name` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/sections/0/name` |
| `start` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/sections/0/start` |
| `duration` | `positive` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/sections/0/duration` |
| `role` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/sections/0/role` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/timeline/sections/0/localizedNames` |

### `tempo`

- **Schema:** `#/$defs/tempo`
- **Type:** `object`
- **必須フィールド:** `at`, `bpm`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `at` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/tempoChanges/0/at` |
| `bpm` | `number` | Yes | maximum=1000; exclusiveMinimum=0 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/tempoChanges/0/bpm` |

### `meter`

- **Schema:** `#/$defs/meter`
- **Type:** `object`
- **必須フィールド:** `at`, `numerator`, `denominator`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `at` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/meterChanges/0/at` |
| `numerator` | `integer` | Yes | minimum=1; maximum=64 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/meterChanges/0/numerator` |
| `denominator` | `integer` | Yes | enum=[1, 2, 4, 8, 16, 32, 64] | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/meterChanges/0/denominator` |

### `timeline`

- **Schema:** `#/$defs/timeline`
- **Type:** `object`
- **必須フィールド:** `sections`, `tempoChanges`, `meterChanges`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `sections` | `array` | Yes | items=#/$defs/section | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/sections` |
| `tempoChanges` | `array` | Yes | minItems=1; items=#/$defs/tempo | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/tempoChanges` |
| `meterChanges` | `array` | Yes | minItems=1; items=#/$defs/meter | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/timeline/meterChanges` |

### `chord`

- **Schema:** `#/$defs/chord`
- **Type:** `object`
- **必須フィールド:** `id`, `start`, `duration`, `label`
- **追加フィールド:** 禁止
- **追加制約:** `dependentRequired`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/id` |
| `start` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/start` |
| `duration` | `positive` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/duration` |
| `label` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/label` |
| `root` | `spelledPitchClass` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/root` |
| `intervals` | `array` | — | minItems=1; maxItems=36; uniqueItems=true; items=integer | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords/0/intervals` |
| `bass` | `spelledPitchClass` | — | — | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) `/harmony/chords/0/bass` |
| `voicing` | `voicing` | — | — | [`03_harmony_only.ostf`](../samples/03_harmony_only.ostf) `/harmony/chords/0/voicing` |

### `scale`

- **Schema:** `#/$defs/scale`
- **Type:** `object`
- **必須フィールド:** `id`, `start`, `duration`, `tonic`, `intervals`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales/0/id` |
| `start` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales/0/start` |
| `duration` | `positive` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales/0/duration` |
| `label` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales/0/label` |
| `tonic` | `spelledPitchClass` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales/0/tonic` |
| `intervals` | `array` | Yes | minItems=1; maxItems=12; uniqueItems=true; items=integer | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales/0/intervals` |

### `harmony`

- **Schema:** `#/$defs/harmony`
- **Type:** `object`
- **必須フィールド:** `chords`, `scales`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `chords` | `array` | Yes | items=#/$defs/chord | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/chords` |
| `scales` | `array` | Yes | items=#/$defs/scale | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/harmony/scales` |

### `instrument`

- **Schema:** `#/$defs/instrument`
- **Type:** `object`
- **必須フィールド:** `name`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `name` | `string` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/instrument/name` |
| `family` | `string` | — | — | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) `/tracks/0/instrument/family` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/tracks/0/instrument/localizedNames` |

### `note`

- **Schema:** `#/$defs/note`
- **Type:** `object`
- **必須フィールド:** `id`, `start`, `duration`
- **追加フィールド:** 禁止
- **説明:** Concrete Core note; optional velocity is a note-on attribute. Phrase templates use a separate version-frozen sourceNote shape.
- **追加制約:** `oneOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/notes/0/id` |
| `start` | `signed` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/notes/0/start` |
| `duration` | `positive` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/notes/0/duration` |
| `pitch` | `pitch` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/notes/0/pitch` |
| `soundSlotId` | `id` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/3/clips/0/notes/0/soundSlotId` |
| `velocity` | `integer` | — | minimum=1; maximum=127 | [`04_melody_only.ostf`](../samples/04_melody_only.ostf) `/tracks/0/clips/0/notes/0/velocity` |

### `clip`

- **Schema:** `#/$defs/clip`
- **Type:** `object`
- **必須フィールド:** `id`, `start`, `duration`, `notes`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/id` |
| `name` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/name` |
| `start` | `nonnegative` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/start` |
| `duration` | `positive` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/duration` |
| `enabled` | `boolean` | — | — | [`10_enabled_states.ostf`](../samples/10_enabled_states.ostf) `/tracks/0/clips/1/enabled` |
| `notes` | `array` | Yes | items=#/$defs/note | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips/0/notes` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/tracks/0/clips/0/localizedNames` |

### `track`

- **Schema:** `#/$defs/track`
- **Type:** `object`
- **必須フィールド:** `id`, `name`, `clips`
- **追加フィールド:** 禁止
- **追加制約:** `allOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/id` |
| `name` | `string` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/name` |
| `role` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/role` |
| `instrument` | `instrument` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/instrument` |
| `enabled` | `boolean` | — | — | [`10_enabled_states.ostf`](../samples/10_enabled_states.ostf) `/tracks/1/enabled` |
| `clips` | `array` | Yes | items=#/$defs/clip | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/tracks/0/clips` |
| `trackType` | `enum` | — | enum=[melodic, drum, mixed] | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/tracks/0/trackType` |
| `preferredView` | `enum` | — | enum=[auto, pianoRoll, drumGrid] | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/tracks/0/preferredView` |
| `soundSource` | `soundSource` | — | — | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/tracks/0/soundSource` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/tracks/0/localizedNames` |
| `drumKitId` | `id` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/tracks/0/drumKitId` |

### `soundSlot`

- **Schema:** `#/$defs/soundSlot`
- **Type:** `object`
- **必須フィールド:** `id`, `name`
- **追加フィールド:** 禁止
- **説明:** Document-local stable logical percussion target. Equal roles, names or MIDI note mappings never merge distinct slot IDs. No sample/audio identity is encoded.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/soundSlots/0/id` |
| `name` | `string` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/soundSlots/0/name` |
| `role` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/soundSlots/0/role` |
| `midiNote` | `pitch` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/soundSlots/0/midiNote` |
| `midiMapping` | `midiMapping` | — | — | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/soundSlots/0/midiMapping` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/soundSlots/0/localizedNames` |

### `lyricDocument`

- **Schema:** `#/$defs/lyricDocument`
- **Type:** `object`
- **必須フィールド:** `id`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/documents/0/id` |
| `title` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/documents/0/title` |
| `language` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/documents/0/language` |

### `lyricPhrase`

- **Schema:** `#/$defs/lyricPhrase`
- **Type:** `object`
- **必須フィールド:** `id`, `documentId`, `text`
- **追加フィールド:** 禁止
- **追加制約:** `dependentRequired`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases/0/id` |
| `documentId` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases/0/documentId` |
| `text` | `string` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases/0/text` |
| `pronunciation` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases/0/pronunciation` |
| `start` | `nonnegative` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases/0/start` |
| `duration` | `positive` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases/0/duration` |

### `lyricUnit`

- **Schema:** `#/$defs/lyricUnit`
- **Type:** `object`
- **必須フィールド:** `id`, `phraseId`, `text`
- **追加フィールド:** 禁止
- **追加制約:** `dependentRequired`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/units/0/id` |
| `phraseId` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/units/0/phraseId` |
| `text` | `string` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/units/0/text` |
| `pronunciation` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/units/0/pronunciation` |
| `start` | `nonnegative` | — | — | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) `/lyrics/units/0/start` |
| `duration` | `positive` | — | — | [`34_all_core_fields.ostf`](../samples/34_all_core_fields.ostf) `/lyrics/units/0/duration` |

### `lyricAlignment`

- **Schema:** `#/$defs/lyricAlignment`
- **Type:** `object`
- **必須フィールド:** `id`, `unitIds`, `noteIds`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/alignments/0/id` |
| `unitIds` | `array` | Yes | minItems=1; uniqueItems=true; items=#/$defs/id | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/alignments/0/unitIds` |
| `noteIds` | `array` | Yes | minItems=1; uniqueItems=true; items=#/$defs/id | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/alignments/0/noteIds` |

### `lyrics`

- **Schema:** `#/$defs/lyrics`
- **Type:** `object`
- **必須フィールド:** `documents`, `phrases`, `units`, `alignments`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `documents` | `array` | Yes | items=#/$defs/lyricDocument | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/documents` |
| `phrases` | `array` | Yes | items=#/$defs/lyricPhrase | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/phrases` |
| `units` | `array` | Yes | items=#/$defs/lyricUnit | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/units` |
| `alignments` | `array` | Yes | items=#/$defs/lyricAlignment | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/lyrics/alignments` |

### `sha256`

- **Schema:** `#/$defs/sha256`
- **Type:** `string`
- **追加制約:** `not`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `pattern="^[0-9a-f]{64}$"`

### `resource`

- **Schema:** `#/$defs/resource`
- **Type:** `object`
- **必須フィールド:** `id`, `kind`, `uri`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/resources/0/id` |
| `kind` | `enum` | Yes | enum=[audio, midi, text, other] | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/resources/0/kind` |
| `uri` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/resources/0/uri` |
| `name` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/resources/0/name` |
| `sha256` | `sha256` | — | — | [`16_generation_context.ostf`](../samples/16_generation_context.ostf) `/resources/0/sha256` |

### `dependencies`

- **Schema:** `#/$defs/dependencies`
- **Type:** `oneOf`
- **追加制約:** `oneOf`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `—`

### `extension`

- **Schema:** `#/$defs/extension`
- **Type:** `object`
- **必須フィールド:** `version`, `status`, `dependencies`, `data`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `version` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/version` |
| `status` | `enum` | Yes | enum=[current, stale, unverified] | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/status` |
| `dependencies` | `dependencies` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/dependencies` |
| `data` | `any` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data` |

### `relativeAssignment`

- **Schema:** `#/$defs/relativeAssignment`
- **Type:** `object`
- **必須フィールド:** `noteId`, `basis`, `contextId`, `anchorPitch`, `index`, `chromaticOffset`
- **追加フィールド:** 禁止
- **追加制約:** `allOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `noteId` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/noteId` |
| `basis` | `enum` | Yes | enum=[chordTone, scaleDegree] | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/basis` |
| `contextId` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/contextId` |
| `anchorPitch` | `pitch` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/anchorPitch` |
| `index` | `integer` | Yes | minimum=1; maximum=36 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/index` |
| `chromaticOffset` | `integer` | Yes | minimum=-24; maximum=24 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments/0/chromaticOffset` |

### `relativeData`

- **Schema:** `#/$defs/relativeData`
- **Type:** `object`
- **必須フィールド:** `assignments`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `assignments` | `array` | Yes | items=#/$defs/relativeAssignment | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1/data/assignments` |

### `generator`

- **Schema:** `#/$defs/generator`
- **Type:** `object`
- **必須フィールド:** `id`, `version`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/generator/id` |
| `version` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/generator/version` |

### `generationInput`

- **Schema:** `#/$defs/generationInput`
- **Type:** `object`
- **必須フィールド:** `role`, `ref`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `role` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/role` |
| `ref` | `ref` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/0/ref` |
| `readRange` | `range` | — | — | [`16_generation_context.ostf`](../samples/16_generation_context.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs/1/readRange` |

### `generationProcess`

- **Schema:** `#/$defs/generationProcess`
- **Type:** `object`
- **必須フィールド:** `id`, `generator`, `inputs`, `outputs`, `protectedNoteIds`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/id` |
| `generator` | `generator` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/generator` |
| `inputs` | `array` | Yes | items=#/$defs/generationInput | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/inputs` |
| `outputs` | `array` | Yes | minItems=1; items=#/$defs/ref | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/outputs` |
| `targetRange` | `range` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/targetRange` |
| `protectedNoteIds` | `array` | Yes | uniqueItems=true; items=#/$defs/id | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/protectedNoteIds` |
| `vendorExtension` | `string` | — | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes/0/vendorExtension` |

### `generationData`

- **Schema:** `#/$defs/generationData`
- **Type:** `object`
- **必須フィールド:** `processes`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `processes` | `array` | Yes | items=#/$defs/generationProcess | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1/data/processes` |

### `color`

- **Schema:** `#/$defs/color`
- **Type:** `string`
- **追加制約:** `not`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `pattern="^#[0-9A-Fa-f]{6}$"`

### `roleDefinition`

- **Schema:** `#/$defs/roleDefinition`
- **Type:** `object`
- **必須フィールド:** `id`, `name`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/roleDefinitions/0/id` |
| `name` | `string` | Yes | minLength=1 | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/roleDefinitions/0/name` |
| `baseRole` | `string` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/roleDefinitions/0/baseRole` |

### `roleColorMap`

- **Schema:** `#/$defs/roleColorMap`
- **Type:** `object`

- **スカラー・分岐制約:** `—`

### `customRoleColorMap`

- **Schema:** `#/$defs/customRoleColorMap`
- **Type:** `object`
- **追加制約:** `propertyNames`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `—`

### `objectColor`

- **Schema:** `#/$defs/objectColor`
- **Type:** `object`
- **必須フィールド:** `target`, `color`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `target` | `ref` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/appearance/objectColors/0/target` |
| `color` | `color` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/appearance/objectColors/0/color` |

### `appearanceData`

- **Schema:** `#/$defs/appearanceData`
- **Type:** `object`
- **必須フィールド:** `roleDefinitions`, `sectionRoleAssignments`, `appearance`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `roleDefinitions` | `array` | Yes | items=#/$defs/roleDefinition | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/roleDefinitions` |
| `sectionRoleAssignments` | `object` | Yes | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/sectionRoleAssignments` |
| `appearance` | `object` | Yes | extra-fields=forbidden | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1/data/appearance` |

### `legacyData`

- **Schema:** `#/$defs/legacyData`
- **Type:** `object`
- **必須フィールド:** `sourceVersion`, `sourceSha256`, `sourceText`, `conversionReport`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `sourceVersion` | `const` | Yes | const="2.3.0" | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) `/extensions/org.ostf.legacy-import.v1/data/sourceVersion` |
| `sourceSha256` | `sha256` | Yes | — | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) `/extensions/org.ostf.legacy-import.v1/data/sourceSha256` |
| `sourceText` | `string` | Yes | minLength=1 | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) `/extensions/org.ostf.legacy-import.v1/data/sourceText` |
| `conversionReport` | `object` | Yes | — | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) `/extensions/org.ostf.legacy-import.v1/data/conversionReport` |

### `extensions`

- **Schema:** `#/$defs/extensions`
- **Type:** `object`
- **追加制約:** `propertyNames`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `org.ostf.relative-phrase.v1` | `allOf` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.relative-phrase.v1` |
| `org.ostf.generation-context.v1` | `allOf` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.generation-context.v1` |
| `org.ostf.appearance.v1` | `allOf` | — | — | [`01_four_part_sketch.ostf`](../samples/01_four_part_sketch.ostf) `/extensions/org.ostf.appearance.v1` |
| `org.ostf.legacy-import.v1` | `allOf` | — | — | [`45_legacy_import_archive.ostf`](../samples/45_legacy_import_archive.ostf) `/extensions/org.ostf.legacy-import.v1` |
| `org.ostf.phrase-library.v1` | `allOf` | — | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1` |
| `org.ostf.clip-choices.v1` | `allOf` | — | — | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1` |
| `org.ostf.localization.v1` | `allOf` | — | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1` |

### `phraseTemplate`

- **Schema:** `#/$defs/phraseTemplate`
- **Type:** `object`
- **必須フィールド:** `id`, `duration`, `notes`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/id` |
| `duration` | `positive` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/duration` |
| `notes` | `array` | Yes | items=#/$defs/phraseSourceNote | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes` |
| `name` | `string` | — | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/name` |

### `phraseNoteMapping`

- **Schema:** `#/$defs/phraseNoteMapping`
- **Type:** `object`
- **必須フィールド:** `templateNoteId`, `repeatIndex`, `noteId`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `templateNoteId` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap/0/templateNoteId` |
| `repeatIndex` | `nonnegative` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap/0/repeatIndex` |
| `noteId` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap/0/noteId` |

### `phraseInstance`

- **Schema:** `#/$defs/phraseInstance`
- **Type:** `object`
- **必須フィールド:** `id`, `templateId`, `clipId`, `repeatCount`, `transpose`, `noteMap`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/id` |
| `templateId` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/templateId` |
| `clipId` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/clipId` |
| `repeatCount` | `positive` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/repeatCount` |
| `transpose` | `integer` | Yes | minimum=-127; maximum=127 | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/transpose` |
| `noteMap` | `array` | Yes | items=#/$defs/phraseNoteMapping | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances/0/noteMap` |

### `phraseLibraryData`

- **Schema:** `#/$defs/phraseLibraryData`
- **Type:** `object`
- **必須フィールド:** `templates`, `instances`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `templates` | `array` | Yes | items=#/$defs/phraseTemplate | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates` |
| `instances` | `array` | Yes | items=#/$defs/phraseInstance | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/instances` |

### `clipCandidate`

- **Schema:** `#/$defs/clipCandidate`
- **Type:** `object`
- **必須フィールド:** `id`, `clipIds`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates/0/id` |
| `clipIds` | `array` | Yes | uniqueItems=true; items=#/$defs/id | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates/0/clipIds` |
| `name` | `string` | — | — | [`55_choice_empty_candidate.ostf`](../samples/55_choice_empty_candidate.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates/0/name` |

### `clipChoiceGroup`

- **Schema:** `#/$defs/clipChoiceGroup`
- **Type:** `object`
- **必須フィールド:** `id`, `candidates`, `selectedCandidateId`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/id` |
| `candidates` | `array` | Yes | minItems=1; items=#/$defs/clipCandidate | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/candidates` |
| `selectedCandidateId` | `any` | Yes | — | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/selectedCandidateId` |
| `name` | `string` | — | — | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups/0/name` |

### `clipChoicesData`

- **Schema:** `#/$defs/clipChoicesData`
- **Type:** `object`
- **必須フィールド:** `groups`
- **追加フィールド:** 禁止

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `groups` | `array` | Yes | items=#/$defs/clipChoiceGroup | [`25_choice_shared_muted.ostf`](../samples/25_choice_shared_muted.ostf) `/extensions/org.ostf.clip-choices.v1/data/groups` |

### `midiMapping`

- **Schema:** `#/$defs/midiMapping`
- **Type:** `object`
- **必須フィールド:** `standard`, `note`
- **追加フィールド:** 禁止
- **追加制約:** `allOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `standard` | `enum` | Yes | enum=[gm1, custom] | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/soundSlots/0/midiMapping/standard` |
| `note` | `pitch` | Yes | — | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/soundSlots/0/midiMapping/note` |

### `soundSource`

- **Schema:** `#/$defs/soundSource`
- **Type:** `object`
- **必須フィールド:** `kind`
- **追加フィールド:** 禁止
- **説明:** Optional informational rendering class or opaque destination hint. Program Change, Bank Select and plugin presets are not standardized by Core.
- **追加制約:** `allOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `kind` | `enum` | Yes | enum=[unassigned, gm1, externalMidi, plugin, hostManaged] | [`61_gm1_drum_grid.ostf`](../samples/61_gm1_drum_grid.ostf) `/tracks/0/soundSource/kind` |
| `identifier` | `string` | — | minLength=1 | [`64_custom_drum_mapping.ostf`](../samples/64_custom_drum_mapping.ostf) `/tracks/0/soundSource/identifier` |

### `localizedNames`

- **Schema:** `#/$defs/localizedNames`
- **Type:** `object`
- **説明:** Optional translations indexed by a conservative BCP 47 language-tag subset. The original name/title remains authoritative and is never rewritten by locale changes. Case-insensitive duplicate tags are forbidden semantically.
- **追加制約:** `propertyNames`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `minProperties=1`

### `drumKit`

- **Schema:** `#/$defs/drumKit`
- **Type:** `object`
- **必須フィールド:** `id`, `name`, `soundSlotIds`
- **追加フィールド:** 禁止
- **説明:** Optional named ordered membership of global Sound Slot IDs. Does not define plugin preset, sample loading, choke or a kit-local MIDI remapping.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) `/drumKits/0/id` |
| `name` | `string` | Yes | — | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) `/drumKits/0/name` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/drumKits/0/localizedNames` |
| `soundSlotIds` | `array` | Yes | uniqueItems=true; items=#/$defs/id | [`43_appearance_all_targets.ostf`](../samples/43_appearance_all_targets.ostf) `/drumKits/0/soundSlotIds` |

### `phraseSourceNote`

- **Schema:** `#/$defs/phraseSourceNote`
- **Type:** `object`
- **必須フィールド:** `id`, `start`, `duration`
- **追加フィールド:** 禁止
- **説明:** Version-frozen 1.0.0-draft.1 phrase source note. Does NOT admit velocity; a linked Core instance may independently set velocity.
- **追加制約:** `oneOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/id` |
| `start` | `signed` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/start` |
| `duration` | `positive` | Yes | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/duration` |
| `pitch` | `pitch` | — | — | [`22_phrase_library.ostf`](../samples/22_phrase_library.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/pitch` |
| `soundSlotId` | `id` | — | — | [`23_phrase_percussion.ostf`](../samples/23_phrase_percussion.ostf) `/extensions/org.ostf.phrase-library.v1/data/templates/0/notes/0/soundSlotId` |

### `voicing`

- **Schema:** `#/$defs/voicing`
- **Type:** `object`
- **必須フィールド:** `pitches`
- **追加フィールド:** 禁止
- **説明:** Optional informational chord voicing snapshot, not a played Core note collection. Repeated pitches and original array order have meaning for lossless preservation. Per-entry velocity is an optional voicing-preview hint.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `pitches` | `array` | Yes | minItems=1; items=#/$defs/pitch | [`03_harmony_only.ostf`](../samples/03_harmony_only.ostf) `/harmony/chords/0/voicing/pitches` |
| `velocities` | `array` | — | minItems=1; items=schema | [`03_harmony_only.ostf`](../samples/03_harmony_only.ostf) `/harmony/chords/0/voicing/velocities` |

### `contributor`

- **Schema:** `#/$defs/contributor`
- **Type:** `object`
- **必須フィールド:** `id`, `name`
- **追加フィールド:** 禁止
- **説明:** One song-local identity; same contributor can receive multiple credit roles. Unicode source name is authoritative.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/contributors/0/id` |
| `name` | `string` | Yes | minLength=1 | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/contributors/0/name` |
| `kind` | `enum` | — | enum=[person, organization, other] | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/contributors/0/kind` |
| `localizedNames` | `localizedNames` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/contributors/0/localizedNames` |

### `credit`

- **Schema:** `#/$defs/credit`
- **Type:** `object`
- **必須フィールド:** `id`, `contributorId`, `role`
- **追加フィールド:** 禁止
- **説明:** Ordered credit assignment referencing one contributor; role is an open semantic token, not a translated label.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/song/credits/0/id` |
| `contributorId` | `id` | Yes | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/song/credits/0/contributorId` |
| `role` | `string` | Yes | pattern="^[a-z][a-z0-9.-]*$" | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/song/credits/0/role` |
| `label` | `string` | — | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/credits/1/label` |

### `alternateTitle`

- **Schema:** `#/$defs/alternateTitle`
- **Type:** `object`
- **必須フィールド:** `id`, `title`
- **追加フィールド:** 禁止
- **説明:** Stable-ID song title alternative; array position and title text do not identify it.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `id` | `id` | Yes | — | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/alternateTitles/0/id` |
| `title` | `string` | Yes | minLength=1 | [`71_multilingual_tracks_and_multiple_drum_kits.ostf`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) `/song/alternateTitles/0/title` |

### `languageMap32`

- **Schema:** `#/$defs/languageMap32`
- **Type:** `object`
- **説明:** Open BCP 47 language-tag dictionary. Schema checks loose subtag structure; full BCP 47 well-formedness and case-insensitive uniqueness are semantic requirements. String may be empty if intentionally blank.
- **追加制約:** `propertyNames`. 正確な述語は末尾の制約一覧とSchemaを参照

- **スカラー・分岐制約:** `minProperties=1`

### `localizationTarget`

- **Schema:** `#/$defs/localizationTarget`
- **Type:** `object`
- **必須フィールド:** `kind`
- **追加フィールド:** 禁止
- **説明:** Stable target by Core ID or profile-local scoped ID; song is the only ID-less target. No array indexes or mutable names as locators.
- **追加制約:** `allOf`. 正確な述語は末尾の制約一覧とSchemaを参照

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `kind` | `enum` | Yes | enum=[song, alternateTitle, contributor, credit, section, track, clip, soundSlot, drumKit, lyricDocument, resource, phraseTemplate, clipCandidate, clipChoiceGroup, roleDefinition] | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries/0/target/kind` |
| `id` | `id` | — | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries/0/target/id` |
| `profile` | `string` | — | enum=[org.ostf.phrase-library.v1, org.ostf.clip-choices.v1, org.ostf.appearance.v1] | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries/0/target/profile` |

### `localizationEntry`

- **Schema:** `#/$defs/localizationEntry`
- **Type:** `object`
- **必須フィールド:** `target`, `field`, `values`
- **追加フィールド:** 禁止
- **説明:** Independent translations for one stable source field. Original source text remains authoritative as fallback.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `target` | `localizationTarget` | Yes | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries/0/target` |
| `field` | `string` | Yes | minLength=1 | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries/0/field` |
| `values` | `languageMap32` | Yes | — | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries/0/values` |

### `localizationData`

- **Schema:** `#/$defs/localizationData`
- **Type:** `object`
- **必須フィールド:** `entries`
- **追加フィールド:** 禁止
- **説明:** Language-map translations indexed by stable kind/id/field, never by names or array positions.

| フィールド | 型 | 必須 | 制約・列挙値 | `.ostf`実例 |
|---|---|---|---|---|
| `entries` | `array` | Yes | items=#/$defs/localizationEntry | [`58_complete_workflow.ostf`](../samples/58_complete_workflow.ostf) `/extensions/org.ostf.localization.v1/data/entries` |

## 条件式・構造分岐の全一覧

次の各位置には条件付き必須、排他条件、拡張プロファイルの選択などの述語があります。条件の正確な内容は3.2 Schemaに記載されています。

| Schema pointer | Rule types |
|---|---|
| `#/$defs/id` | `not` |
| `#/$defs/chord` | `dependentRequired` |
| `#/$defs/chord/properties/intervals` | `contains` |
| `#/$defs/scale/properties/intervals` | `contains` |
| `#/$defs/note` | `oneOf` |
| `#/$defs/note/oneOf/0` | `not` |
| `#/$defs/note/oneOf/1` | `not` |
| `#/$defs/track` | `allOf` |
| `#/$defs/track/allOf/0` | `if,then` |
| `#/$defs/track/allOf/0/then` | `not` |
| `#/$defs/lyricPhrase` | `dependentRequired` |
| `#/$defs/lyricUnit` | `dependentRequired` |
| `#/$defs/sha256` | `not` |
| `#/$defs/dependencies` | `oneOf` |
| `#/$defs/relativeAssignment` | `allOf` |
| `#/$defs/relativeAssignment/allOf/0` | `if,then` |
| `#/$defs/color` | `not` |
| `#/$defs/customRoleColorMap` | `propertyNames` |
| `#/$defs/appearanceData/properties/sectionRoleAssignments` | `propertyNames` |
| `#/$defs/extensions` | `propertyNames` |
| `#/$defs/extensions/propertyNames` | `not` |
| `#/$defs/extensions/properties/org.ostf.relative-phrase.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.relative-phrase.v1/allOf/1` | `if,then` |
| `#/$defs/extensions/properties/org.ostf.generation-context.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.generation-context.v1/allOf/1` | `if,then` |
| `#/$defs/extensions/properties/org.ostf.appearance.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.appearance.v1/allOf/1` | `if,then` |
| `#/$defs/extensions/properties/org.ostf.legacy-import.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.legacy-import.v1/allOf/1` | `if,then` |
| `#/$defs/extensions/properties/org.ostf.phrase-library.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.phrase-library.v1/allOf/1` | `if,then` |
| `#/$defs/extensions/properties/org.ostf.clip-choices.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.clip-choices.v1/allOf/1` | `if,then` |
| `#/$defs/extensions/properties/org.ostf.localization.v1` | `allOf` |
| `#/$defs/extensions/properties/org.ostf.localization.v1/allOf/1` | `if,then` |
| `#/$defs/clipChoiceGroup/properties/selectedCandidateId` | `anyOf` |
| `#/$defs/midiMapping` | `allOf` |
| `#/$defs/midiMapping/allOf/0` | `if,then` |
| `#/$defs/soundSource` | `allOf` |
| `#/$defs/soundSource/allOf/0` | `if,then` |
| `#/$defs/soundSource/allOf/0/then` | `not` |
| `#/$defs/localizedNames` | `propertyNames` |
| `#/$defs/phraseSourceNote` | `oneOf` |
| `#/$defs/phraseSourceNote/oneOf/0` | `not` |
| `#/$defs/phraseSourceNote/oneOf/1` | `not` |
| `#/$defs/voicing/properties/velocities/items` | `anyOf` |
| `#/$defs/languageMap32` | `propertyNames` |
| `#/$defs/localizationTarget` | `allOf` |
| `#/$defs/localizationTarget/allOf/0` | `if,then,else` |
| `#/$defs/localizationTarget/allOf/0/then` | `not` |
| `#/$defs/localizationTarget/allOf/0/then/not` | `anyOf` |
| `#/$defs/localizationTarget/allOf/1` | `if,then,else` |
| `#/$defs/localizationTarget/allOf/1/else` | `not` |

## Schema内の条件判定用フィールド（サンプル不足ではない）

| Schema pointer | 意味 |
|---|---|
| `#/$defs/track/allOf/0/if/properties/trackType` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/relativeAssignment/allOf/0/if/properties/basis` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.relative-phrase.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.generation-context.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.appearance.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.legacy-import.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.phrase-library.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.clip-choices.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/extensions/properties/org.ostf.localization.v1/allOf/1/if/properties/version` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/midiMapping/allOf/0/if/properties/standard` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/soundSource/allOf/0/if/properties/kind` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/localizationTarget/allOf/0/if/properties/kind` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |
| `#/$defs/localizationTarget/allOf/1/if/properties/kind` | 条件式の比較値。実データ側の同名フィールドで代表例を収録 |

## 有限列挙値のサンプル対応

| Schema declaration | Enum choice | 実例 |
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

## 対応範囲・注意事項

現在のパッケージ内の有限なパラメーター宣言・列挙値を一覧化しています。`extensions`の未知名前空間や言語・ID・数値の無限に広がる値空間を全列挙するものではありません。具体例の存在はホストや他ソフトでの実動作を保証しません。

