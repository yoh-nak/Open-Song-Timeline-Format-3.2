# OSTF 3.2.0 — 全網羅ユーザー・実装マニュアル

**対象：** 3.1の曲構成・コード・メロディー・歌詞・演奏ノートを維持し、任意のコード・ボイシング、クレジット、多言語拡張を追加する。規範は[3.2仕様書](FORMAT_SPECIFICATION.ja.md)第18章、構造は[3.2全項目リファレンス](PARAMETER_REFERENCE.ja.md)。下記3.1由来の章は3.2と矛盾しない範囲で利用する。実行による動作検証は行っていない。


## はじめに — 3.2の完全な読み方

このマニュアルは **OSTF 3.2.0のSchema、正式仕様、旧3.0/3.1から継承した任意プロファイル**を横断して説明します。3.2の新規項目はコードボイシング・クレジット・共通Localizationで、従来からある`note.velocity`とドラム管理は継承です。

1. 最初に [`77番`](../samples/77_primary_melody_and_chord_voicing_velocity.ostf) で和声のボイシングと実演奏ノートのVelocityの違いを確認します。
2. [`71番`](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) で曲名・クレジット・キット・トラックの多言語交換を確認します。
3. [`61番`](../samples/61_gm1_drum_grid.ostf)、[`63番`](../samples/63_mixed_track.ostf)、[`64番`](../samples/64_custom_drum_mapping.ostf) でドラムSound SlotとMIDI変換時の分離を確認します。
4. 任意フィールドの型と列挙値は [`パラメーターリファレンス`](PARAMETER_REFERENCE.ja.md)、すべての実フィールドのサンプル場所は [`網羅表`](COVERAGE.md) を使います。
5. 読込専用ツールが理解しない拡張は解釈せず保持します。書き換え・下位版変換時の損失は事前に明示します。

**扱わないもの：** DAWの音源プリセット、完全なドラムサンプル、歌唱合成状態、Program/Bank Change、CC、Pitch Bend、Aftertouch、本格的なミキサー自動化。これらはOSTFの音楽交換という目的の外です。

**文書の優先関係：** 3.2規範仕様書の第18章と3.2 JSON Schemaが、以下の旧版から継承した記述より優先します。

## 旧3.1から継承した構造・操作マニュアル（3.2でも適用）

> **3.1.0版：** 本書は3.0の音楽・profileの説明を引き継ぎ、末尾にトラック種別・MIDI変換の新章を追加しています。元の3.0.0資料は別ファイルで保存。規範要件は3.1.0 Schemaと最新版仕様書を優先します。


本書は、定義済みの全パラメーター群について、何を表し、どの場面で使い、編集時に何を守るかを説明します。ファイルのCore版は **`3.1.0`** です。同梱する6つの任意プロファイルは、従来どおり厳密な版 **`1.0.0-draft.1`** を使用します。Coreの正式版化によって、これらのプロファイルまで正式版になったとは扱いません。

項目ごとの型、必須条件、上限・下限、列挙値、条件分岐、Schema内の位置は、[全パラメーターリファレンス](PARAMETER_REFERENCE.ja.md)で確認できます。[機械可読カタログ](PARAMETER_REFERENCE.ja.md)、[網羅対応表](COVERAGE.md)、[サンプル索引](../samples/INDEX.md)から、定義と実際の記述例をたどれます。規範上の根拠は[仕様本文](FORMAT_SPECIFICATION.ja.md)と[JSON Schema](../schema/ostf-3.1.0.schema.json)です。本書はそれらを解説する資料であり、新しいフィールドや適合要件を追加しません。[English edition](COMPLETE_MANUAL.en.md).

この配布物のサンプルとコマンドは記述例です。今回の正式リリース資料作成では、プログラム、DAW、MIDI、他社ソフトとの相互運用の動作検証を行っていません。項目網羅の記録は文書上の対応関係を示すもので、実行試験の合格記録ではありません。

## 1. OSTFファイルが伝えるもの

OSTFは、元の生成エンジンを再現できないアプリケーションでも、作曲内容を読み取れるようにする交換形式です。曲の区間、コードとスケール、採用済みの各楽器パートのノート、歌詞、素材への参照に加え、必要なら再利用フレーズや演奏候補の関係を保存できます。

採用済みノート演奏の実体は **`tracks → clips → notes`** に入ります。各ノートが持つ中心情報は、発音対象、発音位置、ゲートです。3.1 Coreでは演奏ノートに限り任意のベロシティー（1～127）を認めます。MIDIチャンネル、CC、Bend、Aftertouch、音源状態、オーディオトラック再生、楽譜浄書モデルは定義しません。ホスト側で音色や再生時の強さを決められますが、それらはCoreの共通パラメーターではありません。

| 層 | 書き出す側が保存すること | 読み込む側の扱い |
| --- | --- | --- |
| Core | 作曲者が採用した有限の具体的なノート列 | 保存された音楽を直接読み取る |
| 標準の任意プロファイル | 版、状態、依存先を明示した追加の関係 | 対応する厳密な版だけを解釈する |
| ベンダー拡張 | 生成元固有の設定、プリセット、内部情報 | 不明な値を推測せず保持する |

ファイルを開くだけで、生成、乱数の再抽選、候補切替、フレーズ更新、旧版原文の再解釈を行いません。生成結果を採用するときは、明示的な編集操作でCoreのノートを更新します。任意プロファイルの情報を、Coreと別の重複した演奏として発音させてはいけません。

コードのみ、メロディーのみ、歌詞のみ、空のスケッチも有効な文書です。コードだけのファイルは和声の意図を示し、伴奏ノートが完成済みであるとは表しません。最初は[02 空のスケッチ](../samples/02_empty_sketch.ostf)、[03 コードのみ](../samples/03_harmony_only.ostf)、[04 メロディーのみ](../samples/04_melody_only.ostf)、[05 歌詞のみ](../samples/05_lyrics_only.ostf)が参考になります。

## 2. 必須の11ルート項目から始める

`.ostf`はUTF-8のJSONオブジェクトを1個格納します。配列やオブジェクトが空でも、必須の入れ物は省略しません。

| 項目 | 記述する内容 |
| --- | --- |
| `format` | 厳密に文字列`OSTF`。 |
| `version` | 新規の正式Core文書では`3.1.0`。 |
| `ppq` | 全文書共通の四分音符あたりtick数。整数1～960000。推奨値960。 |
| `song` | 少なくとも`title`を含む曲情報。 |
| `timeline` | `sections`、`tempoChanges`、`meterChanges`。 |
| `harmony` | `chords`と`scales`。 |
| `tracks` | 順序を持つ演奏パートの配列。空でもよい。 |
| `soundSlots` | 打楽器の発音対象定義。空でもよい。 |
| `lyrics` | `documents`、`phrases`、`units`、`alignments`。 |
| `resources` | 素材などへの参照配列。空でもよい。 |
| `extensions` | 名前空間から拡張envelopeへの対応。空の`{}`でもよい。 |

次は、題名をまだ付けていない空のスケッチ全体です。

```json
{
  "format": "OSTF",
  "version": "3.1.0",
  "ppq": 960,
  "song": { "title": "" },
  "timeline": {
    "sections": [],
    "tempoChanges": [{ "at": 0, "bpm": 120 }],
    "meterChanges": [{ "at": 0, "numerator": 4, "denominator": 4 }]
  },
  "harmony": { "chords": [], "scales": [] },
  "tracks": [],
  "soundSlots": [],
  "lyrics": { "documents": [], "phrases": [], "units": [], "alignments": [] },
  "resources": [],
  "extensions": {}
}
```

Coreオブジェクトは、定義されている項目だけを許可します。独自設定はノートやトラックに直接追加せず、名前空間付き拡張の中へ記録します。「任意項目」は省略できるという意味で、通常は`null`を許す意味ではありません。`selectedCandidateId`は、明示的に`null`を許す例外です。

## 3. 数値、識別子、参照の共通規則

### 3.1 整数の表記と精度

厳密な入力ではUTF-8 BOM、不正なUTF-8、オブジェクト内のキー重複、対応しないUnicode surrogate、`NaN`、`Infinity`、非有限数、1つのルートオブジェクト後の空白以外の内容を拒否します。緩いparserで重複キーや数値tokenの元表記が失われてからでは判定できないため、情報を捨てる前に確認します。

整数項目はJSONの整数tokenで記述します。`1`は整数項目に使えますが、`1.0`、`1e0`、`true`は使えません。一般的なJSON Schema実装は、数学的に整数値の実数をintegerとして扱うことがあるため、読み込み時に元のtokenを区別する必要があります。

時刻の共通上限は`MAX = 9007199254740991`です。非負位置は0～MAX、正の長さは1～MAX、ノートの符号付きローカル位置は−MAX～MAXです。音高、PPQ、拍子、移調量などには、それぞれさらに狭い範囲があります。個々の値だけでなく、足し算で得られる発音位置や終端も確認します。

不明な拡張の数値をbinary64の精度に制限しません。`1e400`、`1e-400`、高精度小数も、有限のJSON数値として保持できます。不明データを無損失で保持すると表明するwriterは、数値そのものと整数token／実数tokenの区別を維持する必要があります。空白やオブジェクトキー順を含む完全なバイト一致は別の保証です。[18](../samples/18_opaque_precision.ostf)は精度、[21](../samples/21_integer_boundary.ostf)は整数の境界を扱います。

### 3.2 Core IDと拡張内のローカルID

Coreの全`id`は、オブジェクト種別をまたいで文書内で一意です。1～128文字のASCII文字列で、`^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$`に従います。移動や表示名変更でIDを変えず、独立したコピーには新しいIDを付けます。

テンプレートID、テンプレート内ノートID、インスタンスID、生成プロセスID、独自ロールID、候補グループID、候補IDは、それぞれプロファイルが定めるローカル範囲を使います。テンプレート内のノートIDをCoreノートとして登録しません。ベンダーの`data`を再帰的に走査し、たまたま現れた`id`をCore IDと解釈してはいけません。

### 3.3 型付き参照と範囲

`Ref`は`type`と`id`の2項目を持ちます。`type`の全選択値は、`section`、`track`、`clip`、`note`、`chord`、`scale`、`soundSlot`、`lyricDocument`、`lyricPhrase`、`lyricUnit`、`lyricAlignment`、`resource`です。同じ文字列のIDが別の種別に存在しても、その参照は解決したことになりません。

グローバルな`Range`は`start`と`duration`を持ち、文書のtickを使います。開始は非負、長さは正で、合計がMAXを超えてはいけません。`readRange`と`targetRange`もこの構造です。[41](../samples/41_generation_all_ref_types.ostf)と[43](../samples/43_appearance_all_targets.ostf)には全参照種別の例があります。

## 4. 曲情報とタイムライン

| オブジェクト・項目 | 必須 | 意味と使い方 |
| --- | --- | --- |
| `song.title` | 必須 | 曲名。空文字列も有効。 |
| `song.primaryMelodyTrackId` | 任意 | 主旋律として扱う既存Coreトラック。別の演奏を追加しない。 |
| `sections[].id` | 必須 | 曲の区間のCore ID。 |
| `sections[].name` | 必須 | 「Verse 1」などの空でない表示名。 |
| `sections[].start`、`duration` | 必須 | グローバル開始位置と正の長さ。 |
| `sections[].role` | 任意 | 曲構成上の役割を表す自由な文字列。固定列挙ではない。 |
| `tempoChanges[].at` | 必須 | 次のテンポが始まるグローバルtick。 |
| `tempoChanges[].bpm` | 必須 | 四分音符／分。0より大きく1000以下の有限数。 |
| `meterChanges[].at` | 必須 | 拍子変更の正確なグローバルtick。 |
| `meterChanges[].numerator` | 必須 | 分子。整数1～64。 |
| `meterChanges[].denominator` | 必須 | 分母。1、2、4、8、16、32、64のいずれか。 |

セクションは開始順で、相互に重複しません。隙間や空のセクション配列は許されます。ノートはセクション境界をまたげます。テンポ・拍子の配列には、必ずtick 0の項目を置き、以後の`at`を厳密に増加させます。ホストが推定した小節線へ変更位置を動かしてはいけません。

6/8などの複合拍子でも、BPMは常に四分音符基準です。一定テンポの区間では`秒数 = tick数 × 60 / ppq / bpm`です。テンポ変更をまたぐノートは区間ごとに時間を計算します。拍子変更でtickの意味は変わりません。弱起を第0小節と表示するかなどは、表示上の取り決めです。

PPQ 960では、8分音符のグリッドは480tick、16分音符は240tick、四分音符の3等分は320tickです。7等分は整数tickで正確に表せないため、[07](../samples/07_septuplets.ostf)では適切な解像度を使います。480tickのグリッドに432tickのゲートを置けば90%の長さになりますが、90%は規格共通の既定値ではありません。[11](../samples/11_meter_tempo_gaps.ostf)、[36](../samples/36_all_meter_denominators.ostf)、[48](../samples/48_ppq_minimum.ostf)、[49](../samples/49_ppq_maximum.ostf)も参照してください。

## 5. 和声：ラベル、綴り、音程

`harmony.chords`と`harmony.scales`は、それぞれ`start`の非減少順です。和声コンテキスト同士の重なりは許されます。複数ある場合は、関係を記録する側がIDで対象を選び、配列の偶然の順序に依存しません。

| 構造・項目 | 必須 | 意味 |
| --- | --- | --- |
| 音名`step` | 必須 | A、B、C、D、E、F、Gのいずれか。 |
| 変化量`alter` | 必須 | 整数−2～2。オクターブは含まない。 |
| コード`id`、`start`、`duration` | 必須 | Core IDとグローバル区間。 |
| コード`label` | 必須 | 空でない表示ラベル。ラベルだけのコードも有効。 |
| コード`root`、`intervals` | 対で任意 | 両方を指定するか、両方を省略する。rootは綴り付き音高クラス。intervalsは0を含む空でない配列で、0～35の整数半音距離を重複なく厳密な昇順で並べる。 |
| コード`bass` | 任意 | 綴り付きのベース音高クラス。ベースの発音を生成しない。 |
| スケール`id`、`start`、`duration` | 必須 | Core IDとグローバル区間。 |
| スケール`label` | 任意 | 表示文字列。 |
| スケール`tonic` | 必須 | 綴り付き音高クラス。 |
| スケール`intervals` | 必須 | 0を含む空でない配列。0～11の整数を重複なく厳密な昇順で並べる。 |

自然音の音高クラスはC=0、D=2、E=4、F=5、G=7、A=9、B=11です。`alter`を加算し、12で正規化します。C♯とD♭は音高クラスが同じでも綴りを保持します。

コードの`intervals`にある14は、ルートより上の拡張音程を表すので、勝手に2へ縮約しません。この配列は和声構造であり、実際の演奏ボイシングではありません。Coreノートには、配列にない経過音や不協和音を含められます。和声だけから伴奏を生成しません。

[03](../samples/03_harmony_only.ostf)、[34](../samples/34_all_core_fields.ostf)、[35](../samples/35_pitch_class_spellings.ostf)がラベルのみ・構造化和声を扱います。[12](../samples/12_relative_chord.ostf)、[13](../samples/13_relative_scale.ostf)、[39](../samples/39_relative_objects.ostf)、[40](../samples/40_relative_interval_limits.ostf)では任意の相対音関係を加えています。

## 6. トラック、クリップ、ノート

### 6.1 演奏の全項目

| オブジェクト・項目 | 必須 | 記述する内容 |
| --- | --- | --- |
| トラック`id`、`name` | 必須 | 安定したCore IDと表示名。 |
| トラック`role` | 任意 | melodyやbassなど、ホストが用いる音楽的役割の自由文字列。 |
| トラック`instrument` | 任意 | `name`を必須、`family`を任意とする説明オブジェクト。プラグインを起動しない。 |
| トラック`enabled` | 任意 | 真偽値。省略時の意味はtrue。 |
| トラック`clips` | 必須 | クリップ開始位置の非減少順の配列。 |
| クリップ`id` | 必須 | 安定したCore ID。 |
| クリップ`name` | 任意 | 表示名。 |
| クリップ`start` | 必須 | 非負のグローバルtick。 |
| クリップ`duration` | 必須 | 正の配置・編集上の参照区間長。 |
| クリップ`enabled` | 任意 | 真偽値。省略時の意味はtrue。 |
| クリップ`notes` | 必須 | ローカル開始位置の非減少順の配列。 |
| ノート`id` | 必須 | 発音の各出現に独立して付けるCore ID。 |
| ノート`start` | 必須 | クリップ開始からの符号付きtickオフセット。 |
| ノート`duration` | 必須 | 正のゲート／保持時間。単位はtick。 |
| ノート`pitch` | 発音対象を択一 | 整数0～127。C4=60、A4=69。基準調律はA4=440Hz。 |
| ノート`soundSlotId` | 発音対象を択一 | 既存の打楽器sound slot ID。 |

`pitch`と`soundSlotId`はどちらか一方だけを必ず持ちます。両方指定も両方省略もできません。`instrument.name`、`instrument.family`、Coreの各`role`は説明情報であり、共通の音源ルーティング命令ではありません。アルペジオは奏法であり、楽器名とは別の概念です。

### 6.2 配置区間とゲートを区別する

各ノートについて、次の計算を行います。

```text
絶対発音位置 = clip.start + note.start
絶対ゲート終端 = 絶対発音位置 + note.duration
```

絶対発音位置は非負で、両方の計算結果がMAX以下である必要があります。`clip.start + clip.duration`も確認します。クリップを十分後ろに置けば、負のローカル開始で弱起を表せます。クリップの参照区間より後にノートが始まっても、その先へゲートが伸びてもかまいません。クリップやセクションの境界でノートを切りません。

例えば、開始120・長さ960のクリップに、ローカル開始−120・ゲート240のノートを置くと、絶対位置0～240で演奏されます。ローカル開始720・ゲート480なら、絶対位置840～1320です。クリップの参照区間は1080で終わりますが、後者のノートは1320まで保持されます。

ゲートは「ノートを保持する長さ」であり、音源のリリースや残響が消えるまでの時間、譜面上のタイを含む音価ではありません。同音高のノートが重なっても、別々のIDとゲートを維持します。読み込みで統合しません。[08](../samples/08_pickups_tails.ostf)、[09](../samples/09_same_pitch_overlap.ostf)、[24](../samples/24_phrase_late_overlap.ostf)に具体例があります。

### 6.3 有効状態とイベント順

トラックとクリップの両方がenabledのときだけ、ノートが採用済みの有効演奏に加わります。無効なデータも保存し、構造検査の対象にします。Coreにノート単位muteやsoloはありません。書き出し元は、採用したsolo／mute結果をCoreのenabled状態やノート配列に反映します。

トラック配列の順序は表示順です。クリップとノートは同じ開始位置を許し、その場合の配列順を保持します。イベント抽出の順序は別に定まり、絶対tick、同tickでは全noteOffが全noteOnより前、同tick・同種イベントでは`noteId`の辞書順です。各イベントは`noteId`、`trackId`、`clipId`、発音対象を持ちます。

演奏終端は有効ノートのゲート終端の最大値で、有効ノートがなければ0です。最後のセクションやクリップの終端ではありません。停止・シークや出力先での同音高重複の扱いはホスト側の責任です。[10](../samples/10_enabled_states.ostf)と[47](../samples/47_pitch_boundaries.ostf)は有効状態と音高の境界を扱います。

## 7. 打楽器と参照素材

| オブジェクト・項目 | 必須 | 意味 |
| --- | --- | --- |
| Sound slot `id`、`name` | 必須 | 非音高発音対象のIDと表示名。 |
| Sound slot `role` | 任意 | 自由な役割文字列。同じ役割の複数slotを許す。 |
| Sound slot `midiNote` | 任意 | 整数0～127の出力マッピング用ヒント。 |
| Resource `id` | 必須 | Core ID。 |
| Resource `kind` | 必須 | `audio`、`midi`、`text`、`other`のいずれか。 |
| Resource `uri` | 必須 | 空でない参照文字列。 |
| Resource `name` | 任意 | 表示名。 |
| Resource `sha256` | 任意 | 厳密に64文字の小文字16進数。 |

2つのsound slotが同じroleやMIDI noteヒントを持っていても、別々の対象です。`midiNote`はGeneral MIDI適合の宣言ではありません。具体的な音の割当てはホストが決めます。音源に未割当てのslotでも、保存情報は維持します。半音単位の音高が意図である808ベースなどは`pitch`で表します。[06](../samples/06_percussion_slots.ostf)、[23](../samples/23_phrase_percussion.ostf)を参照してください。

Resourceは生成入力などに使う素材への参照です。記載だけでオーディオトラックを再生したり、自動取得・実行したりしません。参照先が利用できなくても、保存済みノート構造が直ちに無効になるわけではありません。相対URIの解決や素材hashの照合は、明示的なホスト操作として行います。[37](../samples/37_resource_kinds.ostf)に4種類すべての記述例があります。

## 8. 歌詞と多対多の対応

| オブジェクト・項目 | 必須 | 意味 |
| --- | --- | --- |
| 歌詞文書`id` | 必須 | 1つの歌詞文書を表すCore ID。 |
| 歌詞文書`title`、`language` | 任意 | 表示や言語を補助する文字列。 |
| 歌詞句`id`、`documentId`、`text` | 必須 | 句のID、親の歌詞文書、句全体の表示文字列。 |
| 歌詞句`pronunciation` | 任意 | 発音注記。 |
| 歌詞句`start`、`duration` | 対で任意 | 両方指定か両方省略。時刻はグローバル。 |
| 歌詞単位`id`、`phraseId`、`text` | 必須 | 単位のID、親の句、単位の表示文字列。 |
| 歌詞単位`pronunciation` | 任意 | 表示文字列とは独立した発音注記。 |
| 歌詞単位`start`、`duration` | 対で任意 | 両方指定か両方省略。時刻はグローバル。 |
| Alignment `id` | 必須 | 対応関係のCore ID。 |
| Alignment `unitIds`、`noteIds` | 必須 | 既存の歌詞単位IDとCoreノートIDの、順序付き・非空・重複なしの配列。 |

句の表示には句自身の`text`を使い、単位の`text`を連結した結果と一致することを要求しません。言語固有の分かち書きや音素表記は標準化しません。ノートがクリップ相対時刻でも、時刻付きの歌詞句・単位はグローバル座標を使います。

1単位を複数ノートへ、複数単位を1ノートへ、複数同士を対応させられます。メリスマなどを表すために単語やノートを無理に複製する必要はありません。Alignmentはノートを生成せず、歌声合成音源を指定しません。時刻なしの歌詞も有効です。[05](../samples/05_lyrics_only.ostf)、[20](../samples/20_lyric_alignment.ostf)、[46](../samples/46_lyrics_multilingual.ostf)を参照してください。

## 9. 拡張envelopeと編集時の扱い

### 9.1 名前空間とenvelopeの全項目

拡張のキーは逆ドメイン形式で、文法は`^[a-z][a-z0-9-]*(?:\.[a-z][a-z0-9-]*){2,}$`です。少なくとも3つの成分を持ちます。この命名規則は作者の認証ではありません。

| 項目 | 意味 |
| --- | --- |
| `version` | 空でない厳密なプロファイル版文字列。 |
| `status` | `current`、`stale`、`unverified`のいずれか。 |
| `dependencies` | 次の3構造のいずれか。 |
| `data` | 未知プロファイルでは任意のJSON値。対応する厳密版では、そのプロファイルのデータ構造。 |

4項目はすべて必須で、外側envelopeへの独自項目追加はできません。ベンダー固有項目は`data`内に置きます。

| 依存宣言 | 用途 |
| --- | --- |
| `{"scope":"document"}` | 文書全体に依存する場合、または安全に限定できない場合。 |
| `{"scope":"none"}` | 現在のCoreオブジェクトへの依存がない場合。 |
| `{"scope":"objects","refs":[...]}` | 型付き参照を非空・重複なしで明示する場合。 |

対応済みのcurrentプロファイルでobjectsを使う場合、そのdata内にある直接のCore参照をすべて列挙します。プロファイル内ローカルIDはCore参照ではありません。依存には親子関係の影響も含まれます。クリップ移動は子ノートの絶対位置を変え、親のmuteは子ノートの実効有効状態を変えます。必要な依存先を確定できない場合はdocumentを使います。

### 9.2 状態は関係の対応状況を示す

`current`は、保存された関係が現在の文書と対応すると作成側が宣言している状態です。`stale`は、関連する変更によって対応が壊れたと分かっている状態です。`unverified`は、対応を確認していない、または現在の編集側には確認できない状態です。どれもCore以外を演奏の正本に切り替える指示ではありません。currentだけでは未知エンジンの再現を証明しません。

currentの依存参照は解決する必要があります。stale／unverifiedな拡張関係の欠落参照は警告として保持しますが、Core自身の参照切れは常にエラーです。対応する既知版のdata構造が不正なら、どのstatusでもエラーです。状態による緩和は関係の対応確認に関するもので、JSONや既知Schemaの構造を緩和しません。

関連編集後は、理解できる関係と採用済みCore内容を一緒に更新するか、状態をstale／unverifiedへ変更します。未知のdataを推測で変更せず、外側の状態で不確実性を表します。同梱の無効化関数はunverifiedを使います。破綻が分かる編集側は規格の意味に従ってstaleを選べます。状態の更新そのものを、再帰的に別の音楽編集と数えてはいけません。

### 9.3 未知の版と保持

名前空間と厳密な版の組合せで解釈を選びます。知っている名前空間でも将来版ならopaqueとして扱い、旧版dataのSchemaを適用しません。共通envelopeと宣言された依存関係は引き続き解釈できます。

任意プロファイルを1つも理解しないCore readerでも、保存済みノートを扱えます。一方、ある既知プロファイルへの対応を明示する参照validatorは、そのdata構造も検査します。「Coreだけで参加できる」と「同梱validatorが既知拡張の構造不正を無視する」は同じ意味ではありません。

[14](../samples/14_stale_source.ostf)、[15](../samples/15_unverified_source.ostf)、[19](../samples/19_future_profile.ostf)、[38](../samples/38_dependency_scopes.ostf)、[51](../samples/51_opaque_json_values.ostf)、[52](../samples/52_stale_missing_references.ostf)を参照してください。

## 10. 相対音プロファイル

名前空間は **`org.ostf.relative-phrase.v1`** です。`data.assignments`は必須で、空配列も許されます。各assignmentは次の6項目をすべて持ちます。

| 項目 | 意味 |
| --- | --- |
| `noteId` | 音高を持つCoreノート。assignments内で同一対象を重複指定しない。 |
| `basis` | `chordTone`または`scaleDegree`。 |
| `contextId` | chordToneならコードID、scaleDegreeならスケールID。 |
| `anchorPitch` | 実際のオクターブを指定するルート／トニック音高。整数0～127。 |
| `index` | コンテキストの昇順intervals配列の1始まりの位置。構造上の最大値はchordToneが36、scaleDegreeが12。 |
| `chromaticOffset` | 整数−24～24の半音補正。 |

currentでは、indexが実際のintervals配列内に収まり、anchorの音高クラスがroot／tonicと一致し、`anchorPitch + intervals[index − 1] + chromaticOffset`が0～127の保存済みCore pitchと等しくなければなりません。chordToneのコンテキストには構造化されたrootとintervalsが必要です。

indexは配列位置であり、コードネーム上の「5度」などの呼称ではありません。明示的に参照された弱起ノートの発音位置がコンテキスト区間外でも、それだけで不正にはなりません。stale／unverifiedな不一致は警告として保持し、読み込み時にCore pitchを書き換えません。この版は最適な音域、ボイスリーディング、コード追従アルゴリズム、入力ノート順位の解釈を定義しません。サンプル12・13・39・40が対応します。

## 11. 生成コンテキストプロファイル

名前空間は **`org.ostf.generation-context.v1`** です。`data.processes`は必須で、空でもかまいません。

| 構造・項目 | 必須 | 意味 |
| --- | --- | --- |
| Process `id` | 必須 | プロファイル内で一意の生成プロセスID。 |
| Process `generator` | 必須 | 空でない`id`と`version`を持つ生成エンジン識別子。 |
| Process `inputs` | 必須 | 入力配列。空や複数入力を許す。 |
| Input `role` | 必須 | 空でない入力用途文字列。 |
| Input `ref` | 必須 | 型付きCore参照。 |
| Input `readRange` | 任意 | 入力として読んだグローバル範囲。 |
| Process `outputs` | 必須 | 非空の型付きCore参照配列。 |
| Process `targetRange` | 任意 | 変更を要求したグローバル範囲。 |
| Process `protectedNoteIds` | 必須 | 重複のないCoreノートID配列。空でもよい。 |
| Process `vendorExtension` | 任意 | 同じ文書内の別拡張の名前空間。 |

currentでは入力、出力、保護ノート、指定したベンダー拡張が存在する必要があります。vendorExtensionの参照にはdocument依存が必要です。generation-context自身への参照は全statusでエラーです。参照先拡張の欠落やdocumentでない依存は、stale／unverifiedでは警告として保持します。

読み取り範囲、編集要求範囲、結果ノート全体の区間は、弱起や長いゲートによって異なることがあります。複数の入力を、推定した1つの入力へまとめません。seed、complexity、style strength、humanizeなどはエンジン間で同じ意味とは限らないため、独自dataへ置き、エンジンIDと版で意味を特定します。

保護対象について、操作中にID、発音対象、絶対発音位置、ゲート、実効有効状態を維持します。ただし保存済みIDリストだけでは、過去の操作が保護を守ったと証明できません。その主張には操作前後の比較が必要です。[16](../samples/16_generation_context.ostf)、[41](../samples/41_generation_all_ref_types.ostf)、[42](../samples/42_generation_protected_range.ostf)を参照してください。

## 12. 表示・色プロファイル

名前空間は **`org.ostf.appearance.v1`** です。dataには`roleDefinitions`、`sectionRoleAssignments`、`appearance`が必須です。

| 構造・項目 | 意味 |
| --- | --- |
| `roleDefinitions[].id` | プロファイル内で一意の独自ロールID。 |
| `roleDefinitions[].name` | 必須の空でない表示名。 |
| `roleDefinitions[].baseRole` | 任意の自由文字列。ロール色のフォールバックに使う。 |
| `sectionRoleAssignments` | CoreセクションIDから独自ロールIDへのmap。 |
| `appearance.roleColors` | 必須。自由なロール文字列から色へのmap。 |
| `appearance.customRoleColors` | 必須。独自ロールIDから色へのmap。 |
| `appearance.objectColors` | 必須。型付き`target`参照と`color`を持つオブジェクトの配列。 |

空のmapや配列も許されます。色は厳密に`#RRGGBB`形式で、16進文字は大文字・小文字のどちらも使えます。objectColorsに同じ型付きtargetを重複させません。currentの割当てと参照は解決する必要があります。

| 対象 | 左から順に、最初に使える色を採用 |
| --- | --- |
| セクション | 自身の色 → 割当て済み独自ロールの色 → 独自ロールのbaseRole色 → Core section.roleの色 → ホスト既定色 |
| トラック・sound slot | 自身の色 → Core roleの色 → ホスト既定色 |
| クリップ | 自身の色 → 解決済みの親トラック色 |
| ノート | 自身の色 → 解決済みの親クリップ色 |
| その他のCore種別 | 自身の色 → ホスト既定色 |

セクションと時間的に重なっているという理由で、その色をノートへ自動継承しません。stale／unverifiedでも解決可能な色は表示できますが、欠落対象を別オブジェクトへ付け替えません。表示情報は演奏を変えません。この名前空間は2.3の`org.ostf.section-role-appearance.v1`とは別です。[17](../samples/17_appearance.ostf)、[43](../samples/43_appearance_all_targets.ostf)、[44](../samples/44_appearance_inheritance.ostf)を参照してください。

## 13. 再利用フレーズライブラリ

名前空間は **`org.ostf.phrase-library.v1`** です。`data.templates`と`data.instances`が必須で、どちらも空にできます。

| 構造・項目 | 意味 |
| --- | --- |
| Template `id` | ローカルで一意のテンプレートID。 |
| Template `name` | 任意の表示名。 |
| Template `duration` | 文書tickによる正の反復周期。 |
| Template `notes` | 必須。Coreノートと同じ項目構造だが、IDはそのテンプレート内に局所化される。 |
| Instance `id` | ローカルで一意のインスタンスID。 |
| Instance `templateId` | 元テンプレートのローカルID。 |
| Instance `clipId` | 対応を所有するCoreクリップ。 |
| Instance `repeatCount` | 整数1～MAXの有限反復回数。 |
| Instance `transpose` | 整数−127～127の半音移調量。 |
| Instance `noteMap` | 必須の出現対応配列。 |
| Mapping `templateNoteId` | 元テンプレート内ノートのローカルID。 |
| Mapping `repeatIndex` | 0始まりの反復番号。整数0～MAXで、currentではrepeatCount未満。 |
| Mapping `noteId` | 具体化済みCoreノートID。 |

テンプレート自身は演奏されません。currentインスタンスは既存のCoreクリップ1つと厳密に対応し、そのクリップの全ノートをmapで覆います。対応式は次のとおりです。

```text
clip.duration = template.duration × repeatCount
note.start = repeatIndex × template.duration + templateNote.start
note.duration = templateNote.duration
音高ノートのnote.pitch = templateNote.pitch + transpose
非音高ノートのnote.soundSlotId = templateNote.soundSlotId
```

1つのテンプレートを多数のインスタンスが利用できます。1つのクリップを複数インスタンスが所有することはできません。各`(repeatIndex, templateNoteId)`にmapが1つあり、対応するCoreノートIDはライブラリ全体で重複しません。テンプレートIDとインスタンスIDは別のローカル範囲、元ノートIDはテンプレートごとの範囲です。

符号付き開始、周期後の開始、反復間の重なり、弱起、末尾の長いゲートを保持します。移調してもsound slotのIDは変えません。計算結果が音高・時刻の範囲外なら、丸めや切詰めをせず拒否します。空テンプレートには空の実体を作れますが、クリップ区間は時刻範囲内でなければなりません。検査時は、対応数と渡されたmapの一意性によって網羅を確認し、全反復を生成しません。

構造上の曖昧さや所有権の衝突は常にエラーです。リンク切れや実体との不一致はcurrentならエラー、stale／unverifiedなら警告です。外側の1つのstatusが全ライブラリに適用されます。所有対象クリップへmapのない手動ノートを追加した場合、currentな厳密対応ではなくなるので、リンク解除または関係の明示的な更新を行います。依存はdocumentを推奨し、objectsならslot・clip・対応Core noteを列挙します。ローカルIDは列挙しません。

[22](../samples/22_phrase_library.ostf)～24、[30](../samples/30_template_before_materialization.ostf)～33、[53](../samples/53_phrase_empty_template.ostf)、[54](../samples/54_phrase_objects_dependencies.ostf)、[59](../samples/59_phrase_transpose_limits.ostf)を参照してください。この版には、入れ子テンプレート、確率、時間伸縮、コードフィット、ノート単位overrideを定義しません。

## 14. クリップ候補選択

名前空間は **`org.ostf.clip-choices.v1`** です。`data.groups`は必須で、空配列も許されます。

| 構造・項目 | 意味 |
| --- | --- |
| Group `id` | 全グループ内で一意のローカルID。 |
| Group `name` | 任意の表示名。 |
| Group `candidates` | 必須の非空配列。 |
| Group `selectedCandidateId` | 必須。グループ内候補IDまたは明示的な`null`。 |
| Candidate `id` | グループ内で一意のローカルID。 |
| Candidate `name` | 任意の表示名。 |
| Candidate `clipIds` | 必須。CoreクリップIDの順序付き・重複なし配列。空でもよい。 |

グループの管理対象は、全候補のclipIdsの和集合です。同一グループ内でクリップを共有できますが、2つのグループが同じクリップを管理してはいけません。currentでは、各管理クリップ自身のenabled値が、選択候補に含まれるかどうかと一致します。nullなら全管理クリップが無効です。空候補は「名前のある無音案」であり、メタデータ上は未選択とは異なります。

トラックのmuteは独立です。選択クリップでも親トラックが無効なら発音しません。管理外クリップは変更しません。currentの参照と選択は解決する必要があります。stale／unverifiedな不一致は警告ですが、ID重複や所有権衝突は常にエラーです。非選択候補も含め、全候補クリップが依存対象です。

このプロファイルが選ぶのは、すでにCoreへ具体化済みの演奏クリップです。和声、セクション、歌詞、ルーティング、トランスポートを切り替えません。読み込み時の候補適用やフラグ修復も行いません。[25](../samples/25_choice_shared_muted.ostf)、[26](../samples/26_choice_none.ostf)、[55](../samples/55_choice_empty_candidate.ostf)、[56](../samples/56_choice_disjoint_groups.ostf)、[57](../samples/57_choice_objects_dependencies.ostf)を参照してください。

## 15. 旧版原文アーカイブ

名前空間は **`org.ostf.legacy-import.v1`** です。旧版原文を証拠として保存するプロファイルであり、別の演奏の正本ではありません。document依存が必要で、currentを宣言できません。

| 必須のdata項目 | 意味 |
| --- | --- |
| `sourceVersion` | 厳密に`2.3.0`。 |
| `sourceSha256` | 元のUTF-8原文バイト列のSHA-256。64文字の小文字16進数。 |
| `sourceText` | 空でない完全な原文。UTF-8再符号化でhash対象のバイト列を復元する。 |
| `conversionReport` | 変換内容を記録するJSONオブジェクト。wireプロファイルは入れ子の共通レポート項目までは固定しない。 |

元の改行、数値表記、エスケープなどを文字列として保持します。新JSON内でその文字列をエスケープしても、文字列の内容自体は変わりません。後でCoreを編集したとき、その内容が原文にあったかのようにアーカイブを書き換えません。歴史的な別配布converterの通常出力はunverifiedです。定義された規則上、currentでないstale状態も許容されます。[45](../samples/45_legacy_import_archive.ostf)と、別区分の[2.3移行入力](https://github.com/yoh-nak/Open-Song-Timeline-Format-2.3)を参照してください。

## 16. 明示的な編集操作

編集APIはコピーを返し、前後を検査し、呼出側の文書・置換テンプレート・IDリストを変更しません。失敗時に途中まで編集された文書を返しません。以下は同梱実装の契約の説明であり、今回それらを実行したという記録ではありません。詳細とエラーコードは[OPERATIONS.md](COMPLETE_MANUAL.ja.md)にあります。

| API | 入力と結果 |
| --- | --- |
| `convert_version(document, target_version=VERSION)` | Draft 1、Draft 2、正式3.0.0・3.1.0の間を明示変換。既定の行先は3.1.0。 |
| `convert_draft_version(document, target_version)` | 旧互換API。行先引数は必須で、両端をDraft版に限定。 |
| `materialize_phrase(document, template_id, *, instance_id, track_id, clip_id, start, repeat_count=1, transpose=0, enabled=True, max_notes=100000)` | currentライブラリから新しいCoreクリップとインスタンスを追加。 |
| `activate_candidate(document, group_id, candidate_id, *, protected_note_ids=())` | 1グループの候補またはNoneを適用。親muteと管理外クリップを維持。 |
| `replace_phrase_template(document, template_id, replacement, *, protected_note_ids=(), max_notes=100000)` | 完全なテンプレートで置換し、全リンク先を一括更新。 |
| `detach_phrase_instances(document, instance_ids, *, protected_note_ids=())` | 選択インスタンスの関係だけを除去。Coreとテンプレートを維持。 |
| `check_protected_notes(before, after, note_ids)` | 有効かつ同じPPQの前後文書で、固定したノート集合を比較。 |
| `check_generation_protection(before, after, process_id)` | beforeのcurrent生成プロセスから保護集合を固定して比較。 |

フレーズ・候補編集はDraft 2または正式Coreと、対応する厳密なプロファイル版を必要とします。具体化と置換にはcurrentライブラリが必要です。解除は構造的に有効なcurrent／stale／unverifiedを受け付けます。候補適用もこれらの状態を受け付けますが、必要な参照が解決し、操作後に候補プロファイル全体をcurrentとして検査できる必要があります。他グループを副作用として修復しません。

### 16.1 具体化と置換

参照操作が新規採番するノートIDは、空白なしUTF-8 JSON配列`[instanceId, repeatIndex, templateNoteId]`のSHA-256に`note.`を付けたものです。これは操作の採番方針であり、すべての有効Core IDをhash形式にする要件ではありません。Coreノートはローカル開始・反復番号・元配列位置の順、mapは反復番号・元配列位置の順に整列します。

置換入力は同じテンプレートIDを持つ完全な1オブジェクトです。生き残る出現キーは、元mapにあるCore IDをそのまま維持します。他のwriterが付けた独自IDも維持します。元ノートID変更は削除と追加です。操作中は削除分も含めて元Core IDを予約し、衝突があれば全体を拒否します。削除対象ノートを歌詞Alignmentが参照している場合、歌詞を自動削除せず`OPERATION_REFERENCED_NOTE_REMOVAL`で失敗します。

`max_notes=100000`は参照操作の資源制限であり、形式の反復上限やノート数上限ではありません。置換では、全更新対象インスタンスの結果ノート総数を展開前に制限します。1インスタンスでも不正なら全更新を拒否します。空テンプレートで膨大な空反復を列挙しません。完全に同じ置換は順序・状態を保持するno-opコピーです。名前だけの変更ではCoreとmapの順序を保持しつつ、必要なメタデータ・依存状態を更新します。

### 16.2 リンク解除と保護

解除で削除するのは選択したinstance記録だけです。Coreノート、ID、時刻、歌詞、mute、順序、テンプレート、フレーズenvelopeの元status・dependenciesを維持します。APIの空リストは検査済みno-opです。重複IDや欠落IDを含む要求は一括拒否します。最後の不一致を解除してもstaleを自動でcurrentへ上げません。

保護比較は、非有効ノートも含め、存在、対象、絶対開始、ゲート、実効有効状態を比較します。これらが同じなら親を変えてもかまいません。PPQ不一致は`PROTECTION_PPQ_MISMATCH`です。秒数、テンポ、音色、音の減衰まではこのtick基準の比較に含めません。afterから保護リストを消しても、beforeから固定した生成保護比較を回避できません。

実質的な編集は、依存する他のcurrent拡張を保守的に無効化します。no-opで無関係な状態変更を増やしません。サンプル30～33は記述された前後関係、[58](../samples/58_complete_workflow.ostf)は複合利用を扱います。

## 17. 参照readerと対応範囲

同梱の`ostf3.py`はPython 3.10以降と、別途インストールした`jsonschema`を使用する参照ライブラリです。`schema/`ディレクトリを一緒に配置してください。旧編集・移行CLIは100ファイル制限のため収録していません。以下のAPIの意味と正確な規則は正式仕様書を参照してください。

| API | 用途と重要引数 |
| --- | --- |
| `load_strict(source)` | bytesまたはtextを受け、数値tokenの区別を保持して厳密JSONを読み込む。完全なOSTF検査ではない。 |
| `dump_strict(value, *, indent=2, sort_keys=False)` | 対応する厳密JSON値を書き出す。`indent=None`でコンパクト化。これだけでOSTF適合にはならない。 |
| `validate_document(doc)` | wire／メモリー内値、選択Schema、意味規則の診断を返す。 |
| `realized_events(doc)` | 文書検査後、整列済みの有効Coreイベントを返す。 |
| `performance_end(doc)` | 検査後、有効ゲート終端の最大値、または0を返す。 |
| `resolve_color(doc, target, default_color="#808080")` | 型付きCore対象の表示色を解決する。 |
| `invalidate_extensions(doc, changed_refs=None)` | 影響を受けるcurrentをunverifiedにしたコピーを返す。変更参照が分かれば明示し、不明なら省略して保守的に扱う。 |
| `get_schema(version=VERSION)` | 指定した厳密版の同梱Schemaコピー。既定は正式版。 |
| `schema_self_check(version=VERSION)` | 明示的に呼び出されたとき、指定Schemaを確認する。 |

**3.2同梱版の訂正：** 実際に同梱した`ostf3.py`は`VERSION = "3.2.0"`、`CORE31_VERSION = "3.1.0"`、`CORE30_VERSION = "3.0.0"`、`SUPPORTED_VERSIONS = ("3.0.0", "3.1.0", "3.2.0")`です。`DRAFT1_VERSION`と`DRAFT2_VERSION`は同梱コードには存在せず、Draft変換CLIも含まれません。継承する6つの任意profileは`1.0.0-draft.1`、新しい`org.ostf.localization.v1`は`1.0.0`で版選択します。数値tokenを保つには`load_strict`で読んだ結果を`dump_strict`で保存してください。

診断には`severity`、`code`、`path`、`message`があります。pathはJSON Pointerで、空文字列はルートです。英語messageの一致や固定の診断順に依存せず、codeなどの項目を利用します。warningだけで文書が無効になるわけではありません。**同梱ライブラリの例外**は`WireError`と`DocumentValidationError`です。`OperationError`および`MigrationError`は、このZIPにない旧操作・移行ツール側の例外であり、同梱`ostf3.py`からimportできません。インストールや資源不足は音楽形式の診断とは区別します。

対応表明は、Core書出し、Core読込／演奏、不明情報を保持する編集、各プロファイルの解釈、各明示操作、独自エンジンの再生成を分けて記載します。Core-only exporterに全プロファイルの生成は不要です。同梱validatorは対応する厳密版のプロファイル構造を検査するので、より限定されたCore consumerとは検査範囲が異なります。

## 18. コマンドライン引数の全項目

以下は使い方を示すための記述であり、今回の資料作成中に実行したコマンドではありません。すべてのCLIとEditorのsubcommandは、共通の`-h` / `--help`で引数説明を表示できます。

### Validator

`ostf3_validate.py` (historical separate toolkit; NOT bundled here)

| 引数・オプション | 意味 |
| --- | --- |
| `files` | 選択した処理に応じたファイルパス。`-`は標準入力。 |
| `--json` | 機械可読の診断レポート。 |
| `--events` | 整列済み有効Coreイベントを含める。 |
| `--warnings-as-errors` | 診断severity自体を変えず、warningでもコマンドを失敗扱いにする。 |
| `--schema-check` | 同梱Schemaを明示的に確認する。 |
| `--schema-version` | schema-checkで使う厳密な対応root版。既定は正式版。 |

### Editor

書き出すsubcommandは`SOURCE OUTPUT`を取り、共通で、既存の別出力を置換する`--force`と、機械可読receiptを返す`--json`を使えます。forceでも入力ファイルやそのaliasの上書きは許可しません。

| Subcommand | 固有オプション |
| --- | --- |
| `convert` | `--to-version`必須。3.0.0-draft.1、3.0.0-draft.2、3.0.0、3.1.0のいずれか。 |
| `upgrade`、`downgrade` | Draft専用互換コマンド。upgradeの行先はDraft 2、downgradeはDraft 1。 |
| `materialize` | `--template`、`--instance`、`--track`、`--clip`、`--start`必須。任意で`--repeat`（1）、`--transpose`（0）、`--disabled`、`--max-notes`（100000）。 |
| `choose` | `--group`と、`--candidate`または`--none`の択一が必須。任意で反復可能な`--protect-note`、`--protect-process`。 |
| `replace-template` | `--template`、`--replacement` JSONファイル必須。任意で`--max-notes`、反復可能な`--protect-note`、`--protect-process`。 |
| `detach` | 反復可能な`--instance`必須。任意で反復可能な`--protect-note`、`--protect-process`。 |
| `check-protection` | 位置引数`BEFORE AFTER`、`--process`または反復可能な`--note`の択一。任意で`--json`。出力ファイルは作らない。 |

> **旧ツール群の参考仕様：** `ostf3_edit.py`はこの100ファイル版には入っていません。上記は操作の規則を説明するための参考情報であり、同梱コマンドの操作説明ではありません。


置換用の別JSON入力は、完全なテンプレート1個であり、OSTF全文書でも差分patchでもありません。`--protect-process`は入力時点のcurrent生成プロセスから保護対象を取得します。Editorの終了コードは成功0、文書・操作・保護拒否1、引数・I/O・実行環境エラー2です。

出力は全内容を一時ファイルへ書き終えてcloseした後で公開します。forceなしでは出力先が存在しないときだけ作成し、forceありでは別の出力先を原子的に置換します。公開前の失敗では出力先を変更しません。置換テンプレートも含め、入力の直接パス・symbolic link・hard linkを上書き先にできません。必要な原子的公開が使えない場合は部分書込へ切り替えません。電源断耐久性や、公開成功後の失敗を巻き戻す保証までは含みません。

## 19. Root版変換とOSTF 2.3移行

Draftから正式版への変換では、Core内容とプロファイル版を維持します。どちらかの版で未知の拡張は、scopeがnoneでないcurrent宣言をunverifiedにします。既存のstale／unverifiedを自動で昇格させません。opaqueだったdataが変換先で既知になる場合は、変換先の構造に適合する必要があり、推測修復しません。Draft 1が理解するのは元の4プロファイルだけなので、phrase-libraryとclip-choicesはopaqueとして保持します。変換は明示操作であり、読込時に行いません。

OSTF 2.3は別の限定的な移行adapterを使います。既定モードは認識できる題名・タイムライン・対応が確立した表示情報を移し、原文全体を保存します。opaqueな2.3内容から音楽の意味を推測しません。サンプル規約の解釈を明示的に選んだ場合だけ、文書化されたpitch／start／durationやコードsymbolの意味を仮定し、その内容をレポートへ記録します。

| API・オプション | 意味 |
| --- | --- |
| `migrate_bytes(source_bytes, *, interpret_sample_notes=False, target_version=CORE30_VERSION)` | ファイルから始める場合に推奨。意図しない改行正規化を避け、新文書を返す。 |
| `migrate_text(source_text, *, interpret_sample_notes=False, target_version=CORE30_VERSION)` | すでに持っている正確な原文を移行し、新文書を返す。 |
| CLI `SOURCE OUTPUT` | 別々の移行元・移行先`.ostf`ファイル。 |
| `--target-version` | 2.3専用変換器は既定3.0.0、または明示的に3.0.0-draft.2。Draft 1は出力対象外。 |
| `--interpret-sample-notes` | 限定された公開サンプルの規約を明示的に適用する。 |
| `--force` | 入力とそのaliasを保護した上で、既存出力を置換する。 |
| `--json-report` | 埋込み変換レポートを表示する。このモードでの失敗はJSON error object。 |

> **旧ツール群の参考仕様：** 2.3移行CLIと入力ファイルは同梱していません。原典は[別の2.3公開リポジトリ](https://github.com/yoh-nak/Open-Song-Timeline-Format-2.3)を参照してください。


参照adapterのreportには、`conversionMode`、`sourceValidation.scope`・`fullOSTF23ConformanceChecked`・`checks`、`completeMusicMigration`、`assumptions`、`archivedOnly`、`warnings`、`idMappings`、`counts`があります。これらは開いたreport object内の実装レポート項目であり、Coreの固定パラメーター追加ではありません。adapterは原文検査をboundedとし、`completeMusicMigration: false`を記録します。

未知の音楽属性には、mute、移調、強弱などに関わる意味があるかもしれません。限定adapterは、そのようなオブジェクトを有効化せずアーカイブへ残します。コードラベルをroot／intervalsへ解析せず、任意の歌詞を再構成せず、テンプレートや候補群を推定しません。ID衝突は決定的に再採番しますが、曖昧な参照先を推測しません。移行の終了コードは成功0、変換・ファイルエラー1、用法エラー2です。出典と詳細な制限は[3.0元リポジトリ / 3.0 source](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0)、[original sources](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0)を参照してください。

## 20. 作曲時の具体的な使い方

### コード進行から複数パートへ

和声計画をharmonyへ書き、採用したベース、コード、メロディー、打楽器の各ノートをトラックに保存します。役割や楽器の説明を付けますが、その文字列だけに音源ルーティングを依存させません。生成側が関係を把握しているなら、相対音や生成コンテキストを追加します。固有コントロールは独自名前空間へ保持します。元エンジンのアルゴリズムを知らないアプリでも、Coreノートを編集できます。[01](../samples/01_four_part_sketch.ostf)が複数パートのスケッチです。

### 反復フレーズの一部を後から手で直す

テンプレートと具体化済みリンク先を保存します。明示的な置換は、残る出現のIDを維持しつつ全リンク先を更新します。1つの配置だけを独立した手編集として残す場合は、先にそのインスタンスを解除します。Coreノートはそのまま残ります。不一致と分かっているライブラリをcurrentのままにしません。サンプル31～33が各段階を記述しています。

### 演奏案を切り替える

全候補のクリップをCoreへ保存し、それぞれ自身のenabled状態を持たせます。clip-choice groupを追加して採用集合を説明します。候補変更時には明示的にそのフラグを更新します。親トラックのmuteは独立した判断です。別の和声案や曲全体のsceneは、このプロファイルが切り替えると暗黙に扱わず、将来または独自の別定義へ置きます。

### 情報を保つ往復編集

厳密に読み込み、不明値の数値精度を維持し、IDと配列順を保ち、意図したCore編集を行い、依存関係が変わった追加情報を無効化します。明示的な版変換要求がなければ同じroot版で保存します。exporterは限定されたCore書出しを提供できますが、元ソフトの全private設定を保持しない場合、その範囲を明示し、無損失往復と同じ表現を使わないようにします。

## 21. サンプルの読み方と網羅範囲

番号付きの主サンプル76件はすべて拡張子`.ostf`、root版`3.1.0`です。`samples/legacy_3_0/`の3件は未変更の3.0.0原典、2.3原典は別のリポジトリで公開され、このZIPには同梱していません。[サンプル索引](../samples/INDEX.md)には目的と来歴を記録しています。

| 学ぶ内容 | サンプル番号 |
| --- | --- |
| 全体像と作曲途中の各層 | 01～05、34、60 |
| 打楽器、時刻、ゲート、重複、mute、拍子 | 06～11、21、35～37、47～50 |
| 相対音と状態 | 12～15、39～40、52 |
| 生成入力と保護 | 16、41～42 |
| 表示と全Core対象種別 | 17、43～44 |
| 不明データ、数値精度、依存範囲 | 18～19、38、51 |
| 歌詞と原文アーカイブ | 20、45～46。別区分に旧版入力。 |
| フレーズと候補の基本 | 22～26 |
| stale、空、将来版の任意プロファイル | 27～29 |
| 具体化、置換、解除 | 30～33 |
| 空・objects依存のライブラリと移調境界 | 53～54、59 |
| 無音候補、独立グループ、objects依存 | 55～57 |
| 作曲関係の複合利用 | 58 |

本書でいう「全網羅」は、現行版で宣言されたパラメーター位置、有限の列挙値、文書化された構造の分岐を、リファレンスと網羅対応表から追跡できることです。任意文字列、数値の全組合せ、全楽曲、無限に拡張できるベンダーJSONを有限のサンプル群で列挙する意味ではありません。項目がサンプル内に存在することと、意味規則の全条件が実行試験で確認済みであることは区別します。

## 22. 誤りやすい記述

| 誤った扱い | 正しい意味 |
| --- | --- |
| 3.1の演奏ノートへvelocityを足す | 1～127のJSON整数として許可。旧3.0や原型phrase templateには追加不可。 |
| 採用演奏をループrecipeだけで保存する | 有限の実体ノートを保存し、必要ならテンプレート関係を添える。 |
| クリップやセクション境界でノートを切る | ノート開始とゲートを参照区間から独立して扱う。 |
| midiNoteをsound slotのIDにする | IDが識別子で、midiNoteは任意の出力ヒント。 |
| 親トラックmuteだけで候補状態を満たす | 候補対応はクリップ自身のenabledを比較する。 |
| staleなら既知dataの構造不正も許す | 全statusで既知構造を確認する。 |
| 将来版プロファイルへ古いSchemaを適用する | 厳密版で分岐し、不対応payloadをopaqueとして保持する。 |
| 名前の似た対象で欠落Core参照を補う | IDと種別が正確に一致する必要があり、表示名は識別子ではない。 |
| currentだけで過去の生成・保護を証明したとする | 保存された宣言であり、履歴の証明には別の根拠が必要。 |
| 2.3のroot文字列だけを3.0.0へ変える | 限定移行adapterを使い、reportで範囲を把握する。 |
| 項目網羅を相互運用合格と呼ぶ | 網羅は静的な文書対応。実アプリ間の交換は別の主張。 |

## 23. 正式配布の範囲と今後の拡張

初回3.0の範囲は、正式Core、既存の6つの暫定任意プロファイル、文書化した明示操作、保守的な2.3移行です。コード追従フィット、時間伸縮、部分override、曲全体scene、楽譜、追加表現、アプリ固有adapterは今後の課題であり、今回の正式Core配布に暗黙に追加された完了条件ではありません。

配布範囲と検証状況は[公開ノート](../RELEASE_NOTES.md)、適合要件は[正式仕様書](FORMAT_SPECIFICATION.ja.md)、プロジェクトの利用条件は[LICENSE.md](../LICENSE.md)を参照してください。OSTF形式を使用するだけで、独自に作成した曲やアプリケーションへ本プロジェクトのライセンスを課すものではありません。


## 3.1のトラック分類・MIDI出力（新規）

トラック区分・表示ヒント・GM1/customの対応・音源指定の厳密な意味は[正式3.1仕様の第17章](FORMAT_SPECIFICATION.ja.md)、[共通DAWアダプターガイド](COMPLETE_MANUAL.ja.md)を参照してください。全パラメーターは[3.1リファレンス](PARAMETER_REFERENCE.ja.md)、具体例は61〜70に示します。読み込み時に音色設定を勝手に復元するものではありません。


## OSTF 3.1：多言語名称と複数ドラムキットの入力ガイド

- `track.id`は言語に依存しないID、`track.name`は元のUnicodeトラック名。異なるトラックで同名も可能。
- 必要な場合だけ`localizedNames`をトラック／楽器／Sound Slot／クリップ／セクションへ追加。曲タイトルだけ`localizedTitles`を使用する。
- 言語タグはBCP47限定構文。表示時の優先順位は完全一致→末尾サブタグを順に省略→元の名前。機械翻訳は行わない。
- ドラム／パーカッションは複数の独立トラックとして配置できる。`drumKits[]`にキット定義を追加し、各キットの`soundSlotIds[]`へグローバルSlotのIDを順番に格納、`track.drumKitId`で参照する。
- キットは任意。キットを参照するトラックでは、全ての打楽器ノートがそのキットのSlot IDを参照する必要がある。未使用Slotはファイルに残す。
- ドラムグリッドは無効クリップも含む使用中Slotのみを初期表示し、増えた行はスクロールする。「未使用も表示」で定義済みSlotを確認できる。
- Program Change、GM音色プログラム番号、Bank Select、プラグインPresetの共通規格は3.1で設けない。音色はDAWと利用者が設定する。
- 日本語を含む完全な例は[サンプル71](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf)。詳細は[日本語の正式仕様・12.7以降](FORMAT_SPECIFICATION.ja.md#177-多言語名称localizednameslocalizedtitles)を参照。


## OSTF 3.1 正式100ファイル版：データ交換とDAWアダプターの境界

OSTFは**作曲支援データの交換フォーマット**であり、DAWプロジェクトや外部プラグイン内部のシーケンサー設定を復元することは保証しません。読み込むホストは、元の`tracks[]`順序、原本`name`、Track ID、多言語別名を可能な範囲で保持します。音源の実際の割当はホスト・ユーザーが決めます。Drum Rackに相当する音源ではSound Slotの意味と、パッドの**受信MIDIノート番号**を出力先に合わせて割り当てます。Kit IDや行名を使ってサンプルの自動読込・チョーク設定を実行しません。

MIDIへ渡す際は発音対象、絶対開始時刻（`clip.start + note.start`）、ゲート長、明示されたVelocity、元のクリップ／ノートID、無効化内容を可能な限り保持します。PPQ変更による丸め、トラックやSlot IDの消失、重複ノートの合成、未使用キット情報や多言語名の欠落は**損失報告**の対象です。通常の`.mid`だけではOSTF全情報の往復保持は保証できません。

**機能境界：** コード記号だけで演奏ノートを自動生成せず、Primary Melodyは最大1本を指定します。ハモリやバックコーラスの音程は必要なら通常のアレンジトラックで保持します。歌詞は独立データで、ノートIDとの明示的な対応付けを利用します。Velocityは3.1で許可する唯一の新しいノート表現属性であり、CC・Bend・AT・実音色・ミキサー設定は対象外です。出力用の暫定Velocityを原本に書き戻しません。

100ファイル制限により、旧CLIの一部はこの配布物に同梱しません。旧操作の説明は**仕様理解のための参考資料**であり、このZIPに実行可能CLIが含まれることを意味しません。正式な意味規則は[3.1仕様書](FORMAT_SPECIFICATION.ja.md)を優先します。


## 3.2 ボイシング・Velocityの利用手順

1. コードは`harmony.chords[]`の`label`、必要なら`root`と`intervals`で定義する。構成音は演奏情報ではない。
2. 鍵盤やコードクリップに具体的な押さえ方を表示したい場合、`voicing.pitches`に絶対音高の一覧を保存する。たとえば`[48,55,59,64]`。任意の`voicing.velocities`に`[80,null,70,100]`を置けば対応する4音の強弱を示せる。
3. これは表示・試聴候補であり、ロード時の自動発音・演奏トラック作成を意味しない。
4. ピアノ等で実際に演奏するアレンジは従来どおり`tracks[].clips[].notes[]`に保存する。コードボイシングと実ノートのVelocityは自動同期しない。
5. メインメロディーは従来の`primaryMelodyTrackId`で1本を指定する。メロディー各NoteのVelocityは3.1から存在し、3.2でもそのまま利用する。

## 3.2 多言語曲名・クレジットの利用手順

1. 元の曲名を`song.title`に保存し、必要なら`subtitle`、`description`、ID付き`alternateTitles`を追加する。
2. 人物・団体を`contributors[]`で管理し、`song.credits[]`からID参照し、役割・並び順を保持する。
3. `org.ostf.localization.v1`で翻訳対象の安定IDと原文フィールドを指定し、言語タグ`ja`、`en`、`zh-Hant`、`pt-BR`など必要なだけ追加する。
4. 役割`composer`は翻訳しない意味識別子。人名や役割の原文表示ラベルは翻訳できる。
5. 訳詞は題名の翻訳と異なる。音節・発音・ノート対応を別の歌詞データで管理する。
6. 多言語UIを持たないソフトは原文を表示して構わない。ただし再保存時には未対応の翻訳データを黙って破棄しない。

## 3.2 旧版互換の確認項目

- 3.0/3.1の読込時は元の`version`を維持。
- 未指定Velocityや未指定の翻訳・ボイシングを補完しない。
- 旧`localizedNames`/`localizedTitles`と共通翻訳の同じ言語で値が矛盾してはいけない。
- ボイシングの重複音高、配列順、各Velocityとの位置対応、`null`を保持する。
- 3.1以下への書出で落ちる情報は一覧提示し、利用者の許可を得る。
- MIDI、DAW、歌唱・音源の実装や動作は別の話題。

## 3.2 ドラム個別発音・ツール間交換ガイド（仕様18.6–18.7）

OSTFは録音済みドラム音のファイルではありません。**同じ時刻にKickとClapを鳴らすというイベントの区別**をSound SlotのIDで保存し、Player等は各Slotに内蔵音源を割り当てます。LiveのDrum Rackへ貼り付けなくても、Playerが自前で発音できる構造です（Playerがその音源を実装している場合）。

| 場面 | 読込・表示・書出の判断 |
|---|---|
| 36番を`standard:"gm1"`で指定 | GM1 Bass Drum 1を目安にプレビュー音を選んでよい |
| 36番を`standard:"custom"`で指定 | GM1 Bass Drum 1と決め付けない。出力先マップまたはユーザー割当が必要 |
| 2つのSlotが同じrole/番号 | IDごとに演奏イベントを保持。MIDIに統合する際は元IDの消失を報告 |
| Open/Closed Hi-Hat | 別Slotとして独立。チョークの挙動は出力先の音源側で決める |
| `note.duration`が短い | ノートゲートは短いがOne-shotの音声テールを必ず切る指示ではない |
| Slot定義はあるがMIDI対応なし | OSTFファイルは有効。プレビュー音をローカル割当可能。MIDI書出は未解決を報告 |
| Slot自体が見つからない | 文書が不正。別の同名Slotへ自動置換しない |
| 大量トラック／大量Slot | OSTFに本数上限を設けない。Player側はスクロール・ボイス数を管理 |

**推奨するPlayer実装順：** (1)全Slot IDを認識、(2)独立イベントスケジューリング、(3)ユーザー・Playerローカル割当、(4)明示GM1マップから近似音を選択、(5)未解決・制限を見える化、(6)任意のMIDI書出で損失報告。楽器分類用`role`や自由な名前から音色／GM番号を勝手に創作しないこと。音源プリセットやProgram ChangeのCore追加は不要です。

詳しい規範は[18.6–18.7節](FORMAT_SPECIFICATION.ja.md)。[61番](../samples/61_gm1_drum_grid.ostf)は同時に鳴るSnare/ClapとRideを含むGM1の例、[64番](../samples/64_custom_drum_mapping.ostf)は番号36を使ってもGM1ではないCustomの例です。**音声再生の動作検証結果ではありません。**

## OSTF 3.2: Mixedトラックの出力分離、Pad Mute、同一音高の重複

- **発音対象の区別：** `pitch:36`のベース音と、GM1の36番を使うKickは別のノート種別。変換先に応じてトラック・チャンネル・ポートを分ける。 [63番](../samples/63_mixed_track.ostf)では同じ拍に両方が存在する。
- **同一音高の重複：** [69番](../samples/69_muted_and_overlapping.ostf)には同じ音高64で重なり、別IDと別Velocityを持つノートがある。一般的なMIDI1への変換ではNote Offと発音ボイスの対応が曖昧になり得るので、変換上の制約を報告し、元データを短縮・統合しない。
- **ドラム行のMute：** Player内の一時操作。標準CoreにSlot単位の`enabled`はない。文書に保存できるのはトラック／クリップの`enabled`である。
- **Playerの音色割当：** `soundSlotId`は文書内のID。同名・同一IDの別文書へ勝手に音色設定を引き継がない。Player側の文書識別＋Slot IDで管理する。
- **主旋律：** 最大1本を参照し、純粋なドラムトラックを自動的に音程メロディーへ変換しない。

音声再生しないReaderも適合可能。MIDI出力に際して演奏情報を完全に保持できない場合はSidecarで保管するか、情報損失を報告する。詳細は[正式仕様18.8–18.9](FORMAT_SPECIFICATION.ja.md)を参照する。

## 3.2 同梱ライブラリの対応範囲（動作試験の主張ではない）

同梱`ostf3.py`の公開関数には`load_strict`、`dump_strict`、`validate_document`、`realized_events`、`performance_end`、`get_schema`、`schema_self_check`、`resolve_color`、`invalidate_extensions`、`inferred_track_type`、`resolve_track_type`、`resolve_editor_mode`、`choose_display_name`があります。各関数の引数は同梱ソースを正本とします。旧操作Editor、版変換、MIDIファイル出力、2.3移行CLIは**この100ファイル版には入っていません**。これらのAPIを実行して適合認定したわけではありません。

`choose_display_name(obj,locale=...,field=...)`は**3.1由来のオブジェクト内**`localizedNames`／`localizedTitles`を選択する補助関数です。**3.2の`org.ostf.localization.v1`を文書全体から探索する機能はありません**。共通多言語拡張を使って表示するソフトは、規範18.3に従って現在有効な拡張版、安定した対象ID、翻訳可能な原文フィールド、BCP 47タグ、旧フィールドとの競合、原文フォールバックを別途解決します。翻訳UIの実装はOSTF Readerの必須条件ではありません。

本書の3.1時代の章は継承した歴史的説明を含みます。版番号、同梱コマンド、実装API、サンプル数に矛盾がある場合は、3.2追加節と`ostf-3.2.0.schema.json`、実際の同梱ソースを優先します。
