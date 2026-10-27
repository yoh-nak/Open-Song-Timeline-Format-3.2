# Open Song Timeline Format — OSTF 3.2.0

**GitHub仕様公開用パッケージ・100ファイル以内・UTF-8 JSON `.ostf`**

OSTFは、**コード進行・楽曲構成・メインメロディー・歌詞・任意の軽量アレンジ**を作曲支援プラグインや異なるDAWの間で受け渡すフォーマットです。音源プリセット、DAWプロジェクトや本格的なMIDIオートメーションを再現するものではありません。

3.2では3.1の演奏情報を変更せず、**コードに紐付くボイシング（各音のVelocityを含む）**と、**曲名・クレジットなどの統一的な多言語対応**を追加します。メインメロディーは従来どおり1本の指定です。ハモリやバックコーラスを特別な複数メロディー形式にする必要はありません。

## 全網羅資料

| 資料 | 日本語 | English |
|---|---|---|
| 正式仕様書（規範） | [3.2正式仕様](docs/FORMAT_SPECIFICATION.ja.md) | [Specification](docs/FORMAT_SPECIFICATION.en.md) |
| 完全マニュアル | [全網羅マニュアル](docs/COMPLETE_MANUAL.ja.md) | [Complete manual](docs/COMPLETE_MANUAL.en.md) |
| 全パラメーター | [全網羅リファレンス](docs/PARAMETER_REFERENCE.ja.md) | [All parameters](docs/PARAMETER_REFERENCE.en.md) |
| 全項目とサンプル | [対応表](docs/COVERAGE.md) | [サンプル一覧](samples/INDEX.md) |
| 旧版との互換 | [3.0/3.1→3.2](docs/MIGRATION_3_0_TO_3_2.md) | [Migration](docs/MIGRATION_3_0_TO_3_2.md) |
| 構造定義 | [3.2 JSON Schema](schema/ostf-3.2.0.schema.json) | [旧3.1 Schema（未変更）](schema/ostf-3.1.0.schema.json) |

## 3.2で正式化すること

- コードの`harmony.chords[].voicing.pitches`に具体的な音高、`voicing.velocities`に任意の各音Velocity（1〜127、または個別未指定を示す`null`）を保存できます。重複した音高も順番と対応を保持します。これは**ボイシング表示・試聴の候補情報**であり、読込時に演奏ノートや追加トラックを生成しません。
- **メインメロディーと全アレンジの実演奏ノート**の`velocity`（1〜127）は3.1から対応済みで、変更しません。未指定のまま保存できます。
- 楽曲内の人物・団体を`contributors[]`で管理し、`song.credits[]`で作曲者・作詞者・編曲者・演奏者などの役割と順序を記録します。タイトル・副題・説明・別名にも対応します。
- `org.ostf.localization.v1`で任意のBCP 47言語タグを追加できます。言語数も種類も固定しません。旧`localizedNames`等の互換性を維持します。OSTF Playerへの多言語切替UI実装は義務ではありません。
- 歌詞本文の翻訳とノート・発音割当は独立扱いとします。クレジットの翻訳で人のIDを変えません。
- Velocityの追加を理由に、CC、Pitch Bend、AT、Program Change、音源状態やMixerの保存は追加しません。

## 代表サンプル

| サンプル | 内容 |
|---|---|
| [77_primary_melody_and_chord_voicing_velocity.ostf](samples/77_primary_melody_and_chord_voicing_velocity.ostf) | 同一曲に主旋律Velocityとコードの各音Velocity |
| [03_harmony_only.ostf](samples/03_harmony_only.ostf) | 演奏トラックを生成しないコードボイシング |
| [71_multilingual_tracks_and_multiple_drum_kits.ostf](samples/71_multilingual_tracks_and_multiple_drum_kits.ostf) | クレジット、言語別名称、キット・Sound Slot |
| [58_complete_workflow.ostf](samples/58_complete_workflow.ostf) | 既存profileと共通Localizationの共存 |
| [18_opaque_precision.ostf](samples/18_opaque_precision.ostf) | 旧3.1配布例の不正`Infinity`を修正した数値精度例 |

トップレベルのサンプルは**77件**（うち1件は3.1形式を維持）、さらに原典のOSTF 3.0ファイルを3件収録しています。詳細は[全サンプル一覧](samples/INDEX.md)を参照してください。

## 後方互換と公開判定

3.0/3.1のファイルを読んだだけで版番号や内容を書き換えないでください。3.2から旧版への書出では、ボイシングやクレジット、多言語情報の損失を列挙して明示的な許可を求めること。新しい3.2文書を古い3.1のReaderが必ず読めると保証するものではありません。

**公開判定：仕様書・Schema・全網羅資料・サンプルとしてGO。** これは書式・静的整合性の評価です。DAW、Max for Live、MIDI変換、音源の動作や他社実装との交換検証は行っていません。GitHubへのコミットも未実施です。推奨タグ：`v3.2.0`。

[English README](README.md) · [正式リリースノート](RELEASE_NOTES.md) · [ライセンス](LICENSE.md)

## 3.2 ドラム発音・他社ツール互換性の規則を明確化

`Sound Slot ID`を音楽上の発音対象として保持し、MIDI番号やドラムキットの表示行、自由な`role`と混同しないことを規範化しました。同じ拍に別々の打楽器が鳴るノートも、同じSlotの連打・重複も独立したイベントです。`gm1`ならPlayerは対応音色を近似再生できますが、`custom`は同じMIDI番号でもGMの楽器を意味しません。Player内蔵音源はOSTFの外側で個別に割り当てます。One-shotの音声テール、チョーク、サンプル、プリセットはCoreには追加しません。

詳しくは[正式仕様18.6–18.7](docs/FORMAT_SPECIFICATION.ja.md)、[全網羅マニュアルのドラムガイド](docs/COMPLETE_MANUAL.ja.md)、[61番の同時発音](samples/61_gm1_drum_grid.ostf)、[64番のCustom36](samples/64_custom_drum_mapping.ostf)を参照してください。既存の3.2.0形式・パラメーター数・旧3.0/3.1のSchemaを変更しない文書上の明確化です。

## 3.2互換性の追加確定事項（パラメーター追加なし）

[正式仕様18.8–18.9](docs/FORMAT_SPECIFICATION.ja.md)で、Mixedトラックの音程36とGM1 Kick36を別々に扱う規則、同じMIDI番号の重複時の損失報告、Player内だけのPad Mute、文書ごとのSound Slot音色割当を明確化しました。既存の`track.enabled`／`clip.enabled`はそのまま利用し、Slot単位のMuteや音源プリセット、MIDIチャンネルは新設しません。具体例は[63番](samples/63_mixed_track.ostf)と[69番](samples/69_muted_and_overlapping.ostf)。3.2.0のSchemaと100ファイル構成を維持します。

## 3.2 ボイシング表示・同梱Readerの注意

コードのボイシング音高／Velocityは**表示・任意の試聴情報**であり、自動的に実演奏Noteへ展開しない。Song表示とTrack表示の主旋律は同じTrack ID／Note IDを参照する。`ostf3.py`は読込・構造／意味解釈用であり、MIDIファイル生成器や多言語UIではない。同梱`choose_display_name`は3.1由来のローカル名前辞書に限られ、3.2共通Localizationの解決は各ソフトで実装する。[正式仕様18.10](docs/FORMAT_SPECIFICATION.ja.md)、[日本語マニュアル追補](docs/COMPLETE_MANUAL.ja.md)を参照。

## 100ファイル正式版の収録構成

この公開版では3.2 JSON Schemaを正本として、**64件の名前付き定義、242件のプロパティ宣言（229件の実データ項目＋13件のSchema条件判定項目）を再集計**しました。有限の実データ項目229件すべてに、独立した `.ostf` サンプル中の例があります。旧3.1の194項目という数値は3.2全体の数値ではありません。[全網羅表](docs/COVERAGE.md)に正確なSchemaポインターと実例の位置を記載しています。

GitHubでは**ZIPの中身をリポジトリ直下へ展開**します。100ファイルを超える場合、複数回のWebアップロードに分割するか、Gitで一括コミットしてください。同名の公開済みタグがある場合は、配布履歴を保持して新たなリリースを作成してください。
