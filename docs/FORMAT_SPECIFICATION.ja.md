# OSTF 3.2.0 — 正式ファイル仕様書

**第18章の3.2追加規則は、継承した3.0/3.1本文の矛盾する記述より優先する。** 旧章は3.2で変更されない範囲に限り規範として維持する。3.2の構造上の正本は[3.2 JSON Schema](../schema/ostf-3.2.0.schema.json)。公開済み[3.1原典](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.1)は別途保持する。実装・DAWの動作検証は行っていない。

## 3.0 + 3.1から継承する各章（旧バージョン表記は歴史的なもの）

# OSTF 3.1.0 仕様

**3.1.0追補の優先関係：** 3.0 Coreの音楽上の意味を維持し、第17章に明記した追加・変更事項が第2・6・7章の旧記述より優先します。原文の[公開済み3.0.0原典](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0)を別リポジトリに保持し、3.0の公開作業に関する記述は3.1での実行確認を意味しません。

3.1追補策定日：2026年10月26日。Coreのwireバージョン：`3.1.0`。Coreの状態：初回正式仕様。6つの任意profileは、既存の意味を保つ暫定版`1.0.0-draft.1`として同梱する。構造定義は[3.1.0 JSON Schema](../schema/ostf-3.1.0.schema.json)を参照する。[旧Draft 1 Schema（3.1リポジトリ）](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.1)と[旧Draft 2 Schema（3.1リポジトリ）](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.1)は変更せず保持する。

**OSTF 3.1.0の正式Core仕様。任意profileは暫定版のままとする。** 配布資料・Schema・サンプルと参照プログラムの利用条件は[LICENSE.md](../LICENSE.md)、配布範囲は[リリース準備状況](../RELEASE_NOTES.md)に示す。本配布物の作成では動作検証を行っておらず、過去のDraft実行結果を3.0.0の合格実績として扱わない。

OSTF 3.0は、曲構成、和声、歌詞、現在採用している演奏を共有する形式とする。演奏情報の基本は、音高または打楽器の発音対象、開始位置、ゲートタイムであり、3.0の基本仕様にベロシティーを設けなかった。**3.1では実際に演奏するCoreノートに限り任意の`velocity`（1～127）を追加する（第17.11節）。**

本仕様は実装者向けの規則を定める。現在のCoreの型、必須項目、制限値と演奏意味を3.0.0の基準とする。任意profileは名前空間と完全一致する版ごとに解釈し、暫定profileの後続版を無言で同じ意味として扱わない。profileの`current`は保存内容との対応状態であり、profile規格の成熟度を表すものではない。適合判定は、構造、時間、参照、対応する任意仕様を対象とし、音楽的な良さ、同一音色、既存製品での動作を保証するものではない。

3.0.0はDraft 2 Revision 2の音楽的な意味を維持し、正式rootとSchemaを追加する。再利用フレーズとクリップ候補選択、原型templateの置換・全instance更新、リンク解除、保護比較は明示操作として維持する。読み込み時の自動生成や自動同期は追加しない。参照実装は旧2版の読込を残し、3版の明示変換と3.0.0を既定にした2.3移行を用意する。

[複数パート例01](../samples/01_four_part_sketch.ostf)、[リンクしたフレーズ例31](../samples/31_linked_phrase_before.ostf)、[候補選択例25](../samples/25_choice_shared_muted.ostf)を含む、基本60件を継承し、新規16件を加えた計76件の3.1.0記述例を同梱する。全主サンプルのroot版は3.1.0である。別ディレクトリで3.0.0原典3件を区別して保持する。[サンプル索引](../samples/INDEX.md)、[全項目の対応表](COVERAGE.md)で収録範囲を追える。記述例は実行済みの変換結果や試験合格を意味しない。

「必須」「禁止」は実装上の要件、「推奨」は理由があれば別の選択を認める方針を表す。

導入には[最小実装ガイド](COMPLETE_MANUAL.ja.md)、[全網羅マニュアル](COMPLETE_MANUAL.ja.md)、[パラメーターリファレンス](PARAMETER_REFERENCE.ja.md)を用意する。Coreだけを使う入口は[メロディー例04](../samples/04_melody_only.ostf)と[和声例03](../samples/03_harmony_only.ostf)である。説明資料は本仕様とSchemaに従い、新しいフィールドや意味を追加しない。

# 1 対象と三層構造

| 層 | 保存対象 | 基本の読み手との関係 |
| --- | --- | --- |
| Core | 曲情報、時間軸、和声、確定したノート列、歌詞、発音対象と素材参照 | 現在の曲内容を解釈する |
| 標準の任意仕様 | 相対音、生成入力、保護対象、表示情報、再利用フレーズ、クリップ候補 | 対応した読み手が追加の意味を解釈する |
| 独自拡張 | 生成アルゴリズム固有の設定、プリセット、モデル情報など | 未対応の読み手は意味を推測せず保持する |

Coreには、今回採用している有限の曲配置を具体的なノート列として保存する。相対フレーズ、反復、移調、確率的な選択などが現在の演奏に影響する場合、その結果をCoreへ反映する。読み込み時に自動生成を実行したり、候補と確定ノートを二重に演奏したりしてはならない。

コードだけ、メロディーだけ、歌詞だけの未完成文書も有効とする。和声、演奏、歌詞のすべてが完成していることは要求しない。ノートを含まない文書が、完成した演奏を備えているとは扱わない。

3.1 Coreは任意のノートVelocityのみ扱い、CC、ピッチベンド、Aftertouch、Note-off Velocity、音源・エフェクトの状態、音声トラックの再生、無限に続く生成処理、完全な楽譜組版は対象外とする。必要な発展は、Coreの意味を変えない任意仕様として検討する。

# 2 ファイルと最上位構造

拡張子は .ostf とする。内容はUTF-8のJSONオブジェクト一つとする。BOM、同一オブジェクト内の重複キー、不正なUTF-8、孤立したUnicodeサロゲート、NaN、Infinity、数学的に非有限な数値、末尾の余分な非空白内容を禁止する。

Coreおよび対応する既知profileで整数と指定された項目にはJSON整数トークンを使う。1は認め、1.0と1e0は認めない。真偽値を整数として扱わない。JSON Schemaの一般的な整数判定に加えて、この入力時の規則を検証する。

| 必須キー | 型と意味 |
| --- | --- |
| format | 文字列 OSTF |
| version | 文字列 3.1.0 |
| ppq | 四分音符当たりのtick数。整数1〜960000 |
| song | 曲情報オブジェクト |
| timeline | sections、tempoChanges、meterChanges |
| harmony | chords、scales |
| tracks | トラック配列 |
| soundSlots | 打楽器の発音対象配列 |
| lyrics | documents、phrases、units、alignments |
| resources | 生成元などの素材参照配列 |
| extensions | 名前空間別の拡張オブジェクト |

参照読込実装は、rootのversionが`3.0.0-draft.1`、`3.0.0-draft.2`、`3.0.0`、`3.1.0`のいずれかを完全一致で識別し、それぞれのSchemaと対応profile集合を選ぶ。他のroot版は未対応として拒否し、読込時にversionを書き換えない。Draft 1ではrelative、generation、appearance、legacy-importの4つだけを既知とする。Draft 2と3.0.0では、これらにphrase-libraryとclip-choicesを加えた同じ6つを既知とする。後二つはDraft 1文書内では形が分かる場合も未知拡張として保持する。全profileの対応版は`1.0.0-draft.1`のままであり、Coreの正式版化から独立した暫定版である。Schema取得APIおよびSchema確認オプションの既定は3.1.0とし、3.0.0・DraftのSchemaは明示選択できる。

Coreのオブジェクトには、仕様にないキーを直接追加しない。追加情報はextensionsへ入れる。これにより、例えばdurationの綴り間違いを不正とする。3.1で`velocity`を許すのは実演奏Coreノートのみであり、3.0のノートやコード構成音、暫定フレーズ原型などでは未定義のままとする。

配列は原則として空でもよい。ただしtempoChangesとmeterChangesは空にせず、tick 0の値を持たせる。オブジェクトのキー順は意味を持たず、配列順は保持する。時間順が必要な配列は後述の規則に従う。

有限な十進JSON数値は、binary64へ変換したときのオーバーフローやアンダーフローだけで形式不正にしない。例えば未知dataの1e400と1e-400は、元の数値と整数・実数トークンの区別を保持する。Core整数には第3章の上限を適用する。参照実装の数値表現限界を超えた入力は、文法違反ではなく対応範囲の制限として報告する。

未知拡張を無損失で保持すると表明する読み書き実装は、理解していないJSON値の数値精度も失ってはならない。整数トークンと実数トークンの区別も勝手に変えない。通常の浮動小数点数へ変換してから再出力するだけで、無損失を表明してはならない。元の数値トークンまたは必要な精度を保持して、書き戻しを検証する。JSONの空白やキー順まで元のバイト列と同じにすることは別の要件とする。

# 3 時間と識別子

## 共通時間軸

全Core項目および本仕様の標準任意仕様は、文書共通のppqを使う。推奨値は960だが、読者は宣言値に従う。トラック単位のPPQや暗黙の倍率を設けない。

Core整数の絶対的な上限はMAX = 9007199254740991とする。時刻、長さ、加算後の終端もこの範囲内でなければならない。符号を許す位置以外の時刻は0以上、durationは1以上とする。

拍子変更によってtickの単位を変えない。別のPPQへ変換する実装は、整数化で時間を丸める場合、その損失を隠さない。PPQ 960で四分音符を7等分できると仮定せず、必要なら6720など適切なPPQを選ぶ。

## 安定したID

Coreのidは、英数字から始まる1〜128文字のASCII文字列とし、以降は英数字、ピリオド、アンダースコア、コロン、ハイフンを認める。正規表現は次のとおりとする。

`^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$`

全Core種別を通じて、文書内でidを一意にする。表示名、配列番号、トラックの並び順を識別子にしない。同じ対象を移動する際はidを保持することを推奨し、独立した複製には別idを与える。拡張内部のローカルIDは、その任意仕様が定める範囲で一意にする。

## 型付き参照

Refはtypeとidを持つ。typeはsection、track、clip、note、chord、scale、soundSlot、lyricDocument、lyricPhrase、lyricUnit、lyricAlignment、resourceのいずれかとする。

同じidの文字列が別種別に見つかっても、有効な参照とは扱わない。Coreの参照は必ず正しい種別へ解決する。未知拡張のdataを再帰的に探索して、たまたま存在したidをCore IDとして登録してはならない。

# 4 曲情報とタイムライン

## song

titleを必須の文字列とし、未命名の曲では空文字列を認める。primaryMelodyTrackIdは任意で、存在するトラックを参照する。これは主旋律の指定であり、追加の発音命令ではない。

## timeline

| 配列 | 各要素の必須項目 | 任意項目 |
| --- | --- | --- |
| sections | id、空でないname、start、duration | role |
| tempoChanges | at、bpm | なし |
| meterChanges | at、numerator、denominator | なし |

sectionsは開始位置順で、重なりを禁止する。隙間と空配列は認める。roleは開かれた文字列とし、曲構成の種類を固定列挙しない。セクションは全ノートを覆わなくてもよく、境界でノートを切らない。

tempoChangesはat = 0から始まり、atを厳密な昇順とする。bpmは0より大きく1000以下の有限数で、常に四分音符毎分を意味する。各値は次の変更点まで一定とする。連続的なテンポカーブは本仕様では定義しない。

meterChangesもat = 0から始まり、atを厳密な昇順とする。numeratorは整数1〜64、denominatorは1、2、4、8、16、32、64のいずれかとする。変更点を推測した小節境界へ移動してはならない。

一定テンポの区間の経過秒数は、tick数 × 60 ÷ ppq ÷ bpmで求める。テンポ変更をまたぐ音は、区間ごとに時間へ変換する。小節番号や弱起を第0小節と表示する規則は、時間座標と区別する。

# 5 和声

harmonyはchordsとscalesの二つの配列を持つ。どちらも空でよい。各配列はstartの非減少順とし、和声文脈の重なりを認める。複数の解釈がある場合、相対フレーズは対象をIDで選ぶ。並び順だけで使用する解釈を決めない。

## 音名を持つピッチクラス

stepはA〜G、alterは整数−2〜2とする。自然音のピッチクラスをC=0、D=2、E=4、F=5、G=7、A=9、B=11とし、alterを加えて12で正規化する。例えばstep=C、alter=1と、step=D、alter=−1は同じピッチクラスでも綴りを保持する。このオブジェクトはオクターブを含まない。

## コード

Chordはid、start、duration、空でないlabelを必須とする。root、intervals、bassは任意とする。ただしrootとintervalsは両方を持つか、両方を省略する。

rootとbassは音名を持つピッチクラスである。intervalsは根音からの半音距離で、0〜35の整数を重複なく厳密な昇順で並べ、必ず0を含める。14のような拡張音程を2へ自動変換しない。これは宣言された和声解釈であり、実際の演奏ヴォイシングではない。

labelだけのコードも有効とする。読み手独自のコード名解析を、他の読み手と共有できる構造化情報と見なしてはならない。bassを指定しても、ベーストラックを自動生成しない。

## スケール

Scaleはid、start、duration、tonic、intervalsを必須とし、labelは任意とする。tonicは音名を持つピッチクラス、intervalsは0〜11の整数を重複なく厳密な昇順で並べたものとし、0を含む。

コードとスケールは、Coreノートに使ってよい音を制限しない。経過音、不協和音、和声変更の途中状態を形式違反にしない。harmonyだけから発音イベントを生成してはならない。

# 6 トラックとクリップとノート

## トラック

Trackはid、name、clipsを必須とする。role、instrument、enabledは任意とする。instrumentはnameを必須、familyを任意の文字列とする。enabledは省略時trueとする。

メロディー、ベース、伴奏、打楽器は共通のTrackを使う。roleは音楽上の役割、instrumentは楽器の説明であり、いずれも音源プラグインの実行指定ではない。アルペジオなどの演奏方法を楽器名と同一視しない。トラック配列順は表示順として保持する。

## クリップ

Clipはid、start、duration、notesを必須とし、nameとenabledを任意とする。startは曲全体に対する非負の時刻、durationは配置・編集上の基準区間の長さである。enabledは省略時trueとする。

トラック内のクリップは開始位置の非減少順とし、同時開始や重なりを認める。クリップの区間は発音を切るマスクとして使わない。

## ノート

Noteはid、start、durationと、pitchまたはsoundSlotIdのいずれか一つを必須とする。両方を同時に指定しない。

| 項目 | 規則 |
| --- | --- |
| pitch | 0〜127の整数半音番号。60=C4、69=A4。基準の調律ではA4=440 Hz |
| soundSlotId | 存在する打楽器発音対象への参照 |
| start | クリップ基準位置からの符号付き整数tick |
| duration | 1以上の整数tick。ゲート終端までの保持時間 |
| id | 文書内一意のノートID |

クリップ内のノートはstartの非減少順とし、同じstartでは配列順を保持する。発音位置とゲート終端を次のように求める。

`onset = clip.start + note.start`

`gateEnd = onset + note.duration`

onsetは0以上、onsetとgateEndはMAX以下とする。値ごとの範囲検査に加えて、加算結果も検査する。

負のローカル開始、クリップ区間後の開始、クリップを越えるゲート、同じ音高の重なりを認める。各イベントは別のIDとして保持し、読み込み側が勝手に結合や短縮をしない。曲頭の負の絶対時刻は認めず、弱起が必要なら最初のセクションを後ろへ配置できる。

durationは音源の残響やリリースが消えるまでの長さではない。記譜上の音価とも区別する。楽譜上の音価、タイ、声部、細かい奏法は将来の任意仕様で扱う。

## 有効な演奏とイベント順

トラックとクリップの両方がenabledのときだけ、ノートからイベントを出す。無効なトラックやクリップも保存し、構造を検証する。

noteOnをonsetへ、noteOffをgateEndへ出し、両方に同じnoteId、trackId、clipIdを持たせる。同tickではnoteOffを先に並べ、その後noteOnを並べる。同じ種類の同tickイベントはnoteIdの辞書順で決定する。

演奏終端は、有効なノートのgateEndの最大値、ノートがなければ0とする。セクションやクリップの末尾だけを曲の発音終了と見なさない。ユーザーによるトランスポート停止やシークの扱いはホスト側の責任とする。

ソロ状態やノート単位のミュートを持つアプリは、現在採用した演奏がCoreのenabled状態やノート列で分かるように書き出す。同音重複を独立再生できない出力先へ変換する場合は、変換上の制約を扱い、元ノートを書き換えない。

# 7 打楽器と素材参照

## soundSlots

SoundSlotはidとnameを必須とし、roleとmidiNoteを任意とする。midiNoteは0〜127の整数で、特定の出力先に対するヒントにすぎない。GM準拠の宣言や発音対象の識別子として使わない。

同じkickというroleを持つ二つのスロットを別々に保持できる。midiNoteが同じでも結合しない。対応する音源が不明な読み手は、イベントを保持・表示し、ホスト側で音を割り当てる。音色の一致は保証しない。音程を持つ808ベースはpitchを持つノートで表す。

## resources

Resourceはid、kind、uriを必須とする。kindはaudio、midi、text、otherのいずれか、uriは空でない文字列とする。nameと、小文字16進64桁のsha256は任意とする。

これは生成元などの素材参照であり、音声トラックの再生指示ではない。読み込みだけで参照先を実行・取得しない。素材が取得できなくても、保存済みのノート列の構造が不正になるわけではない。sha256の実物照合と、相対URIの基準位置の解決は、明示的な素材処理の機能で行う。

# 8 歌詞

lyricsはdocuments、phrases、units、alignmentsの四つの配列を必須とし、いずれも空を認める。

| 要素 | 必須項目 | 任意項目 |
| --- | --- | --- |
| LyricDocument | id | title、language |
| LyricPhrase | id、documentId、text | pronunciation、start、duration |
| LyricUnit | id、phraseId、text | pronunciation、start、duration |
| LyricAlignment | id、unitIds、noteIds | なし |

documentIdとphraseIdは正しい種別へ解決する。textは表示用文字列、pronunciationは発音用の文字列注記とする。言語ごとの自動分割、音素記号、歌声合成エンジンの動作は、この定義に含めない。languageは補助的な文字列とする。

phraseのtextが表示上の本文であり、unitは分割・割り付け用の注記である。unitを連結するとphraseのtextと完全に一致することを要求しない。発音表記が表示表記と異なる場合も保持する。

phraseとunitに時間を持たせる場合、startとdurationの両方を指定し、曲全体の時間軸を使う。時刻は0以上、長さは1以上、終端はMAX以下とする。

alignmentのunitIdsとnoteIdsは、それぞれ空でなく、各リスト内の重複を禁止し、順序を保持する。複数の歌詞単位と複数のノートを関連付けられる。一音節を複数音へ伸ばす場合も、一音へ複数の単位を対応させる場合も、独立した割り付けとして記録する。alignment自体はノートを生成しない。

# 9 拡張の共通契約

extensionsのキーには逆ドメイン形式の名前空間を使う。例はorg.ostf.relative-phrase.v1である。文法は `^[a-z][a-z0-9-]*(?:\.[a-z][a-z0-9-]*){2,}$` とし、英小文字から始まる三つ以上の区切りを使う。名前空間は識別の慣習であり、作成者の認証を意味しない。

各拡張はversion、status、dependencies、dataを必須とする。外側の項目は固定し、独自項目を追加しない。versionは空でない文字列、dataは任意のJSON値とする。ただし対応している標準任意仕様の版には、その版のdata規則を適用する。

| status | 意味 |
| --- | --- |
| current | 作成側が、現在の文書との対応が一致していると宣言している |
| stale | 関連する変更により、以前の対応が失われたことが分かっている |
| unverified | 対応を確認していない、または現在の編集側では確認できない |

statusはCore演奏を切り替える命令ではない。currentも、未知の生成エンジンを検証器が再現できた証拠にはしない。

dependenciesは、scope=document、scope=none、またはscope=objectsとrefsの組とする。objectsのrefsは空でない型付き参照の配列で、重複を禁止する。noneは、現在のCore対象に依存しないことを意味する。

currentの依存参照は正しい種別へ解決しなければならない。staleまたはunverifiedの欠落参照は、情報を保持して警告とする。Core自身の参照エラーを、この規則で許可してはならない。

Coreへの直接参照を持つ既知の任意仕様がcurrentなら、dependenciesはdocumentにするか、dataにある直接のCore参照先をすべてobjects.refsへ含める。相対ノートの対象と文脈、生成の入力と出力と保護ノート、表示対象などを含む。拡張内部だけのローカルIDは含めない。Coreへの依存がない場合は、個別仕様がdocumentを要求する場合を除きnoneを使える。参照対象の依存は、そのオブジェクトの直接のキーだけでなく、Coreで定義した親子関係による絶対時刻や有効状態にも及ぶ。例えばクリップを移動すれば、そのノートの絶対時刻も変わる。必要な依存を列挙できない場合はdocumentを使う。

関連対象を編集した実装は、理解できる関係と確定ノートを更新するか、statusをstaleまたはunverifiedへ変更する。未知のdataは推測で改変せず、外側のstatusで状態を表す。statusを記録し直すだけの操作を、新たな音楽内容の変更として再帰的に無効化しない。

未知の名前空間も、既知の名前空間の未知バージョンも、基本仕様では保持可能とする。未来のversionを現在のdata定義で検証して拒否してはならない。共通の外側の規則は適用する。

既知の版のdataが構造上不正なら、staleであってもエラーとする。statusによって緩めるのは参照と内容の対応状態であり、既知の構造ではない。

# 10 標準任意仕様

以下の五つと第11章のlegacy-importを合わせた6つのprofileは、暫定版`1.0.0-draft.1`として同梱する。relative、generation、appearance、legacy-importはDraft 1から、phrase-libraryとclip-choicesはDraft 2からの意味と版を維持する。3.0.0のCore正式版化は、これらのprofile版を変更しない。対応する実装はその完全一致版の規則に従い、未対応のCore読者にも保存済みの演奏が分かることを維持する。

## 相対フレーズ

名前空間はorg.ostf.relative-phrase.v1とする。dataはassignments配列を持つ。各要素にはnoteId、basis、contextId、anchorPitch、index、chromaticOffsetを必須とする。

| 項目 | 規則 |
| --- | --- |
| noteId | 対象の音程を持つCoreノート。同じ配列で重複させない |
| basis | chordToneまたはscaleDegree |
| contextId | basisに応じたChordまたはScaleへの参照 |
| anchorPitch | 0〜127の整数。基準となる実際の根音または主音の音高 |
| index | 文脈のintervals配列内の1始まりの位置 |
| chromaticOffset | −24〜24の整数半音補正 |

indexの構造上の上限はchordToneで36、scaleDegreeで12とし、currentでは実際のintervalsの要素数を超えてはならない。ここでのindexは並べられた構成音の位置であり、コード記号の5thなどの名称と同一視しない。

currentでは、Chordにrootとintervalsが必要である。anchorPitchのピッチクラスは、その文脈のrootまたはtonicと一致し、次の計算値が対象ノートのpitchと一致しなければならない。

`pitch = anchorPitch + intervals[index - 1] + chromaticOffset`

計算結果は0〜127でなければならない。明示的な参照がある弱起について、ノート開始が文脈区間の外にあることだけを理由に拒否しない。

staleまたはunverifiedの場合、参照や音高の不一致は警告として保持する。読み込み時に古い相対情報から音高を上書きしてはならない。

新しいコードへ変えたときの最適な音域、声部進行、入力ノートの順位による選択、複雑なFigure解釈は、このrelative profileから自動的には導かない。別の任意仕様または生成側の機能として扱う。

## 生成の文脈

名前空間はorg.ostf.generation-context.v1とする。dataはprocesses配列を持つ。各Processには、拡張内で一意のid、generator、inputs、outputs、protectedNoteIdsを必須とし、targetRangeとvendorExtensionを任意とする。

generatorは空でないidとversionを持つ。inputsの各要素は、空でないrole、型付きref、任意のreadRangeを持つ。inputsは空でも複数でもよい。outputsは空でない型付き参照配列とする。protectedNoteIdsは重複しないCoreノートIDの配列とする。currentでは、入力、出力、保護ノートの参照がすべて正しい種別へ解決しなければならない。staleまたはunverifiedの未解決参照は警告として保持する。

Rangeは曲全体のstartとdurationを持ち、startは0以上、durationは1以上、終端はMAX以下とする。readRangeは参照した文脈、targetRangeは変更を求めた範囲であり、弱起や伸ばした音を含む出力範囲とは区別する。

currentでvendorExtensionを使う場合、同じ文書内に存在する別の拡張を参照し、dependencies.scopeをdocumentにする。自分自身への参照はstatusによらず禁止する。staleまたはunverifiedでは、参照先の欠落またはdocument以外のscopeを警告として保持する。

保護指定を理解して生成操作を実行するアプリは、その操作で保護したノートのID、発音対象、曲全体での開始時刻、ゲート、有効状態を保持する。ノートのローカル値を変えずに親クリップを動かす、親を無効化する、ノートを削除することも、保護した内容を変更する操作に含む。検証器は参照を検査できるが、過去の生成処理が正しく保護を守ったかは、その前後データなしには判定しない。

seed、complexity、humanizeなどの生成設定を、エンジンに依存しない同じ動作と定義しない。固有設定は独自拡張へ置く。この任意仕様は生成処理を実行する命令ではない。

## 表示と構成役割

名前空間はorg.ostf.appearance.v1とする。dataにはroleDefinitions、sectionRoleAssignments、appearanceを必須とする。

roleDefinitionsの各要素は拡張内一意のidと空でないnameを持ち、baseRoleは任意の文字列とする。sectionRoleAssignmentsは、CoreセクションIDからカスタム役割IDへの対応表とする。

appearanceにはroleColors、customRoleColors、objectColorsを必須とする。前二つは役割名またはカスタム役割IDから色への対応表、objectColorsはtargetという型付き参照とcolorを持つ配列とする。色は#RRGGBB形式とし、大文字・小文字の16進数字を認める。同じ対象へのobjectColorsの重複は認めない。

currentなら対象の参照とカスタム役割を解決する。セクションの色は、対象自身のobjectColors、割り当てた役割のcustomRoleColors、その役割のbaseRoleに対応するroleColors、Core section.roleに対応するroleColors、ホスト既定色の順で、最初に見つかった指定を使う。トラックとSoundSlotは、対象自身のobjectColors、Core roleに対応するroleColors、ホスト既定色の順とする。クリップは対象自身の色、次に親トラックの解決済みの色を使う。ノートは対象自身の色、次に親クリップの解決済みの色を使う。その他の対象は対象自身の色、なければホスト既定色とし、時間上で重なったセクションから色を推測しない。staleまたはunverifiedでも解決できる色の参照は表示できるが、未解決参照は別の対象へ結び直さない。

この名前空間は2.3のorg.ostf.section-role-appearance.v1とは別とする。旧版の対象種別や意味を無言で変更しない。

## 再利用フレーズ

名前空間は`org.ostf.phrase-library.v1`、版は`1.0.0-draft.1`とし、Draft 2または3.0.0文書で解釈する。dataは`templates`と`instances`の配列を必須とし、どちらも空でよい。各レコードはSchemaに定めた項目だけを持つ。

Templateは`id`、`duration`、`notes`を必須、`name`を任意とする。durationは文書共通PPQの正の整数tickで、**反復周期**を表す。nameは表示用文字列である。template IDはtemplates内で一意にする。

templateのnotesはCore Noteと同じ項目形、すなわち`id`、`start`、`duration`と、`pitch`または`soundSlotId`のいずれか一つを持つ。ただしIDはそのtemplate内で一意なローカルIDであり、Coreノートとして登録しない。notesはstartの非減少順とする。負のローカル開始、周期後の開始、周期を越えるゲートを認める。templateにあるだけのノートは発音しない。soundSlotIdはCoreへの直接参照である。

Instanceは次の項目をすべて必須とし、追加項目を認めない。

| 項目 | 規則 |
| --- | --- |
| id | instances内で一意のローカルID |
| templateId | 同じprofile内のtemplate ID |
| clipId | 配置先のCore clip ID |
| repeatCount | 1〜MAXの整数反復回数 |
| transpose | −127〜127の整数半音移調量 |
| noteMap | 発生ごとにtemplateNoteId、repeatIndex、noteIdを持つ配列 |

noteMapのrepeatIndexは0〜MAXの整数、templateNoteIdは参照template内のローカルノートID、noteIdはCore note IDとする。noteMapの配列順は対応の意味を変えない。すべてのIDには第3章のID文法を使う。templateとinstanceのID集合は別のローカル範囲である。

一つのinstanceが一つのCore clipを所有する。複数instanceから同じclipIdを指定してはならない。templateは複数instanceで共有できる。template ID、instance ID、同一template内のノートID、同一instance内の`(repeatIndex, templateNoteId)`、profile全体のmapped Core note IDの重複、およびclip所有の競合は、statusにかかわらずエラーとする。

currentではtemplateIdを解決し、repeatIndexをrepeatCount未満にする。templateの全ノートと全反復の直積に対して、noteMapが過不足なく一対一に対応しなければならない。mapped noteはそのinstanceのclip内に存在し、そのclipにあるすべてのノートがmapに含まれる。未対応の追加ノートを同じclipへ混ぜたままcurrentとしない。

各instanceの一致条件は次のとおりとする。

```text
clip.duration = template.duration × repeatCount
note.start = repeatIndex × template.duration + templateNote.start
note.duration = templateNote.duration
pitched note.pitch = templateNote.pitch + transpose
unpitched note.soundSlotId = templateNote.soundSlotId
```

soundSlotIdにはtransposeを適用しない。計算されたpitchは0〜127とする。clip.startが曲全体の配置を与え、絶対開始とゲート終端はCoreの式で求める。乗算後のclip長、計算されたローカル開始、絶対開始と終端もCoreの範囲を満たす。最初の反復の弱起や最後の反復のテールを省略・短縮せず、各発生を独立したCore IDとして保存する。

検証器は期待される全発生を生成してはならない。必要件数を整数で計算し、与えられたmapの各キーの有効性、一意性、件数によって完全な対応を検査できる。参照実装の明示的実体化操作には別に資源上限を設けるが、それを形式上の反復回数上限と混同しない。

参照欠落、template欠落、mapの対応不足・過剰、対象clip・時刻・長さ・計算音高・範囲の不一致は、currentではエラー、staleまたはunverifiedでは警告として保持する。既知の構造、ローカルIDや所有の曖昧さ、Core自身の不正をstatusで許容しない。外側のstatusは全instanceへ適用する。

依存対象には全参照soundSlot、clip、mapped Core noteを含め、ローカルIDは含めない。document scopeを推奨する。Core依存がない空のライブラリーなどではnoneを使える。読み込み時に実体化や自動refreshを実行しない。ネストしたtemplate、時間伸縮、確率、和声追従、部分overrideはこの版に含めない。相対フレーズや生成元の規約とは別のprofileであり、両方をcurrentにするならそれぞれの関係が同じCoreと一致する必要がある。

## クリップ候補の選択

名前空間は`org.ostf.clip-choices.v1`、版は`1.0.0-draft.1`とし、Draft 2または3.0.0文書で解釈する。dataは`groups`配列だけを持ち、空を認める。

Groupは`id`、`candidates`、`selectedCandidateId`を必須、`name`を任意とする。group IDはgroups内で一意とし、candidatesは空にしない。Candidateは`id`、`clipIds`を必須、`name`を任意とする。candidate IDはそのgroup内で一意のローカルIDであり、Core IDではない。nameは表示用文字列である。

clipIdsはCore clip IDの順序付き・重複なし配列とする。無音の候補を表す空配列を認める。selectedCandidateIdはそのgroup内のcandidate IDまたはnullとする。nullは明示的に「どの候補も選ばない」状態である。

groupが管理する集合は、全candidateのclipIdsの和集合とする。同じgroup内の複数候補で一つのclipを共有できるが、異なるgroupが同じclipを管理してはならない。group ID、group内のcandidate ID、候補内のclip IDの重複と管理所有の競合は、statusにかかわらずエラーとする。

currentでは選択IDと全候補のCore clip参照を解決する。管理下の各clip自身のenabledは、選択されたcandidateに含まれるかどうかと一致しなければならない。enabled省略時はtrueとして比較する。selectedCandidateIdがnullなら管理下の全clipを無効にする。

比較対象は**clip自身のenabled**であり、親trackとの積で決まる有効状態ではない。track.enabledは独立したミュート状態で、候補選択操作では変更しない。したがって選択されたclipでも、親trackが無効なら発音しない。未管理のclipにも触れない。異なるclipの時間上の重なりはCoreと同じく認め、曲構成や時間帯から管理groupを推測しない。

選択先candidateの欠落、Core clip参照の欠落、選択とenabledの不一致は、currentではエラー、staleまたはunverifiedでは警告として保持する。未選択を含む全candidateのclip参照を依存対象にする。読み込み時に選択を適用したり、Coreのenabledを修正したりしてはならない。

このprofileが選ぶのは、Coreに既に実体化された**演奏clipの組合せ**である。和声、セクション、歌詞文書、routing、トランスポートを含む全曲シーンは選択しない。Core harmonyにenabledを導入したことにはならない。

## 明示操作と操作前後の保護比較

参照操作APIは[操作ガイド](COMPLETE_MANUAL.ja.md)、[フレーズ・候補profileガイド](FORMAT_SPECIFICATION.ja.md)、[参照実装ガイド](COMPLETE_MANUAL.ja.md)を参照する。操作は呼出側の入力を変更せずコピーを返し、操作前後を検証する。置換templateやIDリストも呼出側のものを変更しない。失敗時に部分的に変更された文書を返さない。未対応のroot版、名前空間、profile版に対して意味を推測して操作しない。未知payloadの値と数値tokenはコピーと書き戻しを通じて保持する。

`convert_version(document, target_version=VERSION)`は`VERSION = "3.0.0"`を既定とし、Draft 1、Draft 2、3.0.0の正確なroot版の間で明示的に変換する。Core内容とイベントを変えず、root版と必要な拡張statusだけを更新する。変換元か変換先で未知となるcurrent拡張は、依存scopeがnoneでない場合unverifiedにする。両側が理解する既存profileの意味と版は変えず、staleやunverifiedをcurrentへ自動昇格しない。未知だったdataが変換先で既知の構造となり不正と判明する場合は、変換に失敗する。読み込みだけでこの処理を行わない。

互換APIの`convert_draft_version(document, target_version)`はtargetを必須とし、変換元・変換先の両方をDraft 1またはDraft 2に限定する。CLIの`convert SOURCE OUTPUT --to-version VERSION`は3版を扱う。旧`upgrade`はDraft 2へ、旧`downgrade`はDraft 1へ向けた旧2版間だけの別名であり、正式版への変換には`convert`を使う。

`materialize_phrase`はDraft 2または3.0.0のcurrentな既知phrase profileから、新しいCore clipとinstanceだけを追加する。既存clip、instance、Core IDを上書きしない。テンプレート内ノート数×反復回数が参照実装のmax_notes（既定100000）を超える場合、展開前に拒否する。この上限は実装資源の制限である。参照操作のノートIDは`[instance_id, repeatIndex, templateNoteId]`のコンパクトJSONをUTF-8化したSHA256に`note.`を付け、既存IDと衝突すれば拒否する。notesは`(start, repeatIndex, template内順)`、mapは`(repeatIndex, template内順)`で並べる。空templateは空clipを作れる。親trackのenabledを変えず、影響する他の拡張を無効化し、更新後のphrase全体を検証してからcurrentとする。この作成操作は既存instanceを再構築しない。Revision 1の明示的な原型置換は次節で定め、読込時の自動伝播は引き続き行わない。

`activate_candidate`はDraft 2または3.0.0の対応版clip-choicesを対象に、指定groupの全管理clipのenabledと選択IDを一括で更新する。構造が有効ならprofileがstaleやunverifiedでも明示操作を試せるが、必要な管理参照は解決できなければならない。他のgroupを勝手に修復せず、操作後の全groupがcurrentとして検証できない場合は操作全体を失敗させる。Coreが変化しないcurrentの同一選択では、無関係な拡張を無効化しない。固定したprotected_note_idsを指定した場合、変更後に保護比較を行い、不一致なら部分結果を返さない。

`check_protected_notes`は、呼出時に固定した重複のないnote ID集合を、検証済みのbeforeとafterで比較する。無効なノートも除外せず、IDの存在、pitchまたはsoundSlotId、曲全体での開始、durationとゲート終端、trackとclipを合わせた有効状態を比較する。ローカル座標や親のID自体は同一でなくてもよい。PPQが異なる場合はPROTECTION_PPQ_MISMATCHとして不一致とする。空の集合はcheckedNoteIdsが空であることを明示する。

`check_generation_protection`は、beforeのcurrentかつ対応版のgeneration profileの一つのprocessから保護IDを固定して、上記比較へ渡す。after側でprocessや保護リストを削除・変更しても保護対象を減らせない。この比較は二つの保存状態における定義済み項目の一致を示すだけであり、生成エンジンの実行や過去の操作履歴を証明しない。テンポ変更による秒数の変化、音色、音声結果はtick単位の保護契約に含めない。

## 原型の置換とリンク解除（Revision 1）

以下は既存phrase-libraryの保存形式を使う明示的な編集操作であり、profileの新しい意味やフィールドを定めるものではない。両操作とも正確なDraft 2または3.0.0 rootと対応版のphrase-libraryを要求する。操作前後の全体検証、呼出側入力を変更しないこと、失敗時に部分結果を返さないことを共通契約とする。protected_note_idsは操作前に固定した重複のないlistまたはtupleとし、不一致ならOPERATION_PROTECTION_VIOLATIONとして結果を返さない。

### replace_phrase_template

`replace_phrase_template(document, template_id, replacement, *, protected_note_ids=(), max_notes=100000)`は、原型一つを置き換え、そのtemplateIdを使う**全instance**を一括更新する。入力文書は有効で、phrase-library全体がcurrentでなければならない。staleな実体化に隠れた手編集を上書きしない。対象templateは既に存在し、replacementは既存Schemaに従う完全なtemplateオブジェクトで、そのidがtemplate_idと一致する必要がある。差分パッチや暗黙の改名として扱わない。構造不正、ローカルノートID重複、欠落したsoundSlotを拒否する。

各instanceのid、templateId、clipId、repeatCount、transposeを保ち、親track、Core clipのid・start・name・自身のenabled、trackのenabledを保持する。template.durationとnotesの変更に応じて既存の反復式を再計算し、clip.durationとnote列・noteMapを更新する。他のtemplate、instance、Core clipは保存されたまま保持する。

存続する`(repeatIndex, templateNoteId)`には、**操作前の有効なnoteMapにあるCore noteId**を使う。独自の書出し実装が付けたIDも保持し、ハッシュ形式へ置き換えない。新しい発生だけ、materializeと同じ決定的なIDを作る。今回削除されるIDも含めた操作前の全Core IDと、今回の新規IDを予約し、衝突すれば操作全体を拒否する。source noteのID変更は削除と追加であり、音高・時刻が同じという理由で同一性を推定しない。

更新時のnotesは`(ローカル開始, repeatIndex, source配列順)`、mapは`(repeatIndex, source配列順)`で並べる。弱起、テール、周期後の開始、重なりをそのまま扱う。全affected instanceの**更新後ノート総数**を展開前にmax_notesと比較する。既定100000は操作の資源制限で、wire上限ではない。空templateでは巨大な空反復を列挙しない。どれか一つの配置で音高、clip区間、ローカル開始または絶対終端が不正になる場合も、全体を失敗させる。

削除されるCore noteを歌詞alignmentのnoteIdsが参照している場合は、OPERATION_REFERENCED_NOTE_REMOVALと対象を示す診断を返して失敗する。歌詞unit、alignment、参照を自動削除しない。存続するnoteへの歌詞参照は同じIDのまま保持する。参照が保持されても、音高やゲートが不変だという意味ではないため、必要な不変条件は固定した保護比較で検査する。

その他の拡張payloadは保持する。Core変更は変更clipと削除noteの参照を使って保守的に無効化する。削除したnoteの親関係が変更後に分からない場合など、無効化は直接の対象より広くなり得る。profileだけの実質的変更でも、他のnone以外のcurrent宣言を保守的にunverifiedへ変更する。更新後のphrase依存をdocumentとし、全profile検証と保護比較に成功した結果だけを返す。

完全に同じ有効なreplacementは、保護も検証した上でstatus、dependencies、配列順を変えないno-opコピーとする。durationとnotesが同じでnameだけが異なる場合は、Coreと既存noteMapの順序を保持し、metadata変更に必要なstatus・依存の更新だけを行う。読み込み時の同期や、無関係なstale関係の修復は行わない。

### detach_phrase_instances

`detach_phrase_instances(document, instance_ids, *, protected_note_ids=())`は、選択したinstanceレコードだけを削除してリンクを解除する。instance_idsは有効なIDの重複のないlistまたはtupleで、要求したすべてのinstanceが存在しなければならない。有効なIDと欠落IDが混ざる要求を部分実行しない。空リストは文書と保護を検証したno-opコピーとする。

入力phraseはcurrent、stale、unverifiedのいずれでもよいが、文書全体の構造と必須Core整合性は有効でなければならない。すべてのtemplateとCoreをそのまま残し、ノートID、clip、時刻、歌詞alignment、和声、配列順、clip自身のenabled、trackミュートを変更しない。staleなリンクが付いた手編集済みclipも、再生成せずそのまま独立させられる。

リンク除去というprofile編集により他のnone以外のcurrent宣言を保守的に無効化するが、phrase自身のstatusは**操作前の値を保持**する。除去後に不一致が消えても、staleやunverifiedをcurrentへ昇格しない。phraseの既存dependenciesも保持し、必要以上に広い依存宣言を勝手に縮めない。結果全体と固定保護集合を検査してから返す。無関係な関係の修復、Coreノート・歌詞参照の削除は行わない。

### ファイル操作の保護

`replace-template`のSOURCEと`--replacement`のtemplate JSONは、両方とも上書き禁止の入力である。既存出力は明示的な--forceが必要で、--forceでも入力自身・ハードリンク・シンボリックリンクを出力先にできない。replacementファイルは厳密なUTF-8 JSONオブジェクト一つとして読み込む。`detach`も同じ原子的な出力規則を使う。CLIの--protect-processは操作前のcurrent generation processからだけ保護IDを取得する。出力は完全な内容を準備してから公開し、検証・操作・保護比較・準備書込・公開処理が公開完了前に失敗した場合、最終出力先を変更しない。必要な原子的公開が利用できなければ、非原子的な方法へ切り替えず失敗する。公開後の障害の巻き戻しを保証するものではない。

# 11 2.3からの移行

2.3の公開仕様は、内部の音高や歌詞割り付けを全面的には規範化していない。したがってversionを変更するだけでは3.0へ変換できない。参照：[2.3 Format Specification](https://github.com/yoh-nak/Open-Song-Timeline-Format-2.3/blob/90fce246de28a1b265fb6b25cc2866c70036fda6/docs/FORMAT_SPECIFICATION.md)、[2.3 Parameter Reference](https://github.com/yoh-nak/Open-Song-Timeline-Format-2.3/blob/90fce246de28a1b265fb6b25cc2866c70036fda6/docs/PARAMETER_REFERENCE.md)。

## 標準の移行範囲

移行器は、既知で正しく解釈できる曲名とタイムライン構造を基本変換とする。出力先の既定は`3.0.0`とし、APIの`target_version=VERSION`またはCLIの`--target-version`で`3.0.0-draft.2`も明示選択できる。この移行器はDraft 1を出力先にせず、Draft 1からの保守的な解釈方針は変えない。内部の演奏、コード名解析、歌詞の参照を、一般的な意味として勝手に推測しない。正しい3.0構造へ収まらない対象は理由を記録する。2.3から再利用templateや候補groupを自動的に発見・生成しない。

2.3の元ファイルをUTF-8文字列のsourceTextとして保存し、そのUTF-8バイト列のSHA256とsourceVersionを付ける。保存先はorg.ostf.legacy-import.v1、版は1.0.0-draft.1、statusはunverified、dependenciesはdocumentとする。conversionReportには変換済み、未解釈、仮定、警告、ID対応を残す。

sourceTextは原典アーカイブであり、もう一つの再生用正本ではない。3.0編集に合わせて自動更新しない。元ファイルが保持されたことと、すべての音楽的意味が変換されたことは区別する。

## サンプル規約を明示的に解釈するモード

明示的なinterpret-sample-notesの選択がある場合だけ、2.3サンプルで使われるpitchをC4=60の整数音高、ノートstartをクリップ相対、durationをゲートと解釈する。同様にsymbolをlabelとして扱う場合も、この仮定を変換報告へ明記する。

未知の演奏属性により、ミュート、移調、強弱などの意味が未解釈になる可能性がある対象は、変換済みとして黙って有効化しない。推測変換を見送り、原典と理由を保持する。特定アプリに確定した規約がある場合、後から個別の移行アダプターを追加できる。

旧IDが種別間で衝突する場合、3.0の文書内一意IDへ決定的に対応付ける。旧データ内で曖昧な参照は推測で結び直さない。

## 色と役割の移行

旧仕様で意味が確定している役割・色の情報は、対象IDの対応が分かる範囲で新しいappearanceへ移す。変換先のノートやクリップが存在しないとき、その対象色を別の対象へ強引に割り当てない。未変換の情報は原典と報告に残す。見た目の一致は旧規約の優先順位まで確認した場合にのみ表明する。

3.0から2.3への完全な可逆変換は保証対象にしない。3.0の新規演奏の正本はtracksに一本化する。歌詞の多対多構造やコードlabelを3.0で表せることと、任意の2.3内部データの意味を変換できることは別である。

# 12 最小構成を拡張した記述例

次の例は、4拍のクリップに3音を置き、最後の音がクリップ末尾を越えて伸びる構成を示す。クリップ末尾はtick 3840、最後のゲート終端はtick 4320である。E4の相対指定は、CのanchorPitch 60にintervalsの2番目である4を加えて64となる。

相対フレーズを理解しない読み手も、Coreに保存された60、64、67の音高と時間を読み取れる。任意仕様は追加のノートを発生させない。

```json
{
  "format": "OSTF",
  "version": "3.0.0",
  "ppq": 960,
  "song": {
    "title": "First OSTF 3.0 Sketch",
    "primaryMelodyTrackId": "track-lead"
  },
  "timeline": {
    "sections": [
      {
        "id": "section-verse",
        "name": "Verse",
        "start": 0,
        "duration": 3840,
        "role": "verse"
      }
    ],
    "tempoChanges": [
      {
        "at": 0,
        "bpm": 120
      }
    ],
    "meterChanges": [
      {
        "at": 0,
        "numerator": 4,
        "denominator": 4
      }
    ]
  },
  "harmony": {
    "chords": [
      {
        "id": "chord-cmaj7",
        "start": 0,
        "duration": 3840,
        "label": "Cmaj7",
        "root": {
          "step": "C",
          "alter": 0
        },
        "intervals": [
          0,
          4,
          7,
          11
        ]
      }
    ],
    "scales": []
  },
  "tracks": [
    {
      "id": "track-lead",
      "name": "Lead",
      "role": "melody",
      "clips": [
        {
          "id": "clip-lead",
          "start": 0,
          "duration": 3840,
          "notes": [
            {
              "id": "note-c4",
              "pitch": 60,
              "start": 0,
              "duration": 720
            },
            {
              "id": "note-e4",
              "pitch": 64,
              "start": 960,
              "duration": 720
            },
            {
              "id": "note-g4",
              "pitch": 67,
              "start": 1920,
              "duration": 2400
            }
          ]
        }
      ]
    }
  ],
  "soundSlots": [],
  "lyrics": {
    "documents": [],
    "phrases": [],
    "units": [],
    "alignments": []
  },
  "resources": [],
  "extensions": {
    "org.ostf.relative-phrase.v1": {
      "version": "1.0.0-draft.1",
      "status": "current",
      "dependencies": {
        "scope": "document"
      },
      "data": {
        "assignments": [
          {
            "noteId": "note-e4",
            "basis": "chordTone",
            "contextId": "chord-cmaj7",
            "anchorPitch": 60,
            "index": 2,
            "chromaticOffset": 0
          }
        ]
      }
    }
  }
}
```

# 13 適合試験の基準

以下は既存の意味要件を具体化した受入条件である。[適合性の範囲と全89観点](FORMAT_SPECIFICATION.ja.md)は期待する挙動を示し、実行済み・合格済みの意味ではない。今回の配布資料作成では動作検証を行わず、その追加実施を公開条件に加えない。構造、厳密JSON入力、意味検証、確定イベント列、移行の各層を分け、JSON Schema単体の成功を全項目の合格としない。

| ID | 入力または操作 | 期待結果 |
| --- | --- | --- |
| C01 | 空のtracks、harmony、lyricsと正しいtempo・meter | 有効な未完成文書 |
| C02 | コードなしのメロディー | 有効 |
| C03 | ノートなしの歌詞と空のalignments | 有効 |
| C04 | 同一IDをtrackとnoteに使用 | Core参照の前にID重複エラー |
| C05 | primaryMelodyTrackIdがclipを指す | 種別違いエラー |
| C06 | PPQ 6720、四分音符内の7音を960tick間隔で配置 | 丸めず有効 |
| C07 | PPQ 0または宣言と違う暗黙960換算 | 不正または実装不適合 |
| C08 | clip.start=960、note.start=−120 | onset=840として保持 |
| C09 | clip.start=0、note.start=−120 | 負の絶対開始エラー |
| C10 | 最後のゲートがclip末尾を越える | 短縮せず保持 |
| C11 | 同じ音高の二音が重なる | 別IDのまま両方保持 |
| C12 | 同tickで前音が終わり次音が始まる | noteOffを先に出す |
| C13 | enabled=falseのtrackまたはclip | 保存・検証し、発音しない |
| C14 | クリップの基準区間より後にノートが開始 | 絶対時刻が有効なら保持 |
| C15 | 各値は範囲内だが終端の加算がMAXを超える | 終端範囲エラー |
| C16 | **3.0.0** Coreノートにvelocityを直接追加 | 3.0 Schemaでは未定義項目としてエラー。**3.1.0**の演奏ノートは1～127の整数のみ受理 |
| C17 | pitchとsoundSlotIdを同時指定、または両方なし | 構造エラー |
| C18 | 非GMのmidiNoteを持つ二種類のkick | 独立スロットとして保持 |
| C19 | 一音節を複数ノートへ割り付ける | 参照が正しければ有効 |
| C20 | labelだけのコードを通常表示する | 有効。構造化コードと誤認しない |
| C21 | labelだけのコードをcurrentなchordToneの基準にする | 必要な構造不足エラー |
| C22 | current相対指定の計算結果とnote.pitchが違う | 対応不一致エラー |
| C23 | 同じ不一致をstaleまたはunverifiedとして保持 | 警告。ノートを書き換えない |
| C24 | current既知profileで依存参照を記載し忘れる | dependenciesの不足エラー |
| C25 | 未知の名前空間または既知名前空間の未来版 | 共通外側を検証し、dataは未対応として保持 |
| C26 | staleだが既知profileの必須フィールドが欠ける | 構造エラー |
| C27 | 異なる二つの入力を持つ生成記録 | 入力を一つに潰さない |
| C28 | 存在しないprotectedNoteIdをcurrentで参照 | 参照エラー |
| C29 | 同じ表示対象へ二つのobjectColorsを指定 | 重複対象エラー |
| C30 | 未知拡張を含む文書を関連編集する | dataを保持し、未確認のcurrentを残さない |
| C31 | ランダム生成済みノートを読み直す | 再抽選しない |
| W01 | 重複JSONキー、BOM、不正UTF-8 | 入力時に拒否 |
| W02 | NaN、Infinity、非有限の数値 | 入力時に拒否 |
| W03 | 整数フィールドへ1.0、1e0、true | 入力または型検証で拒否 |
| W04 | 未知dataに高精度の数値 | 無損失を表明する書き手は丸めない |
| M01 | 2.3を標準モードで変換 | 内部ノートの意味を推測しない |
| M02 | サンプル規約解釈を明示して変換 | 適用した仮定を報告する |
| M03 | 原典アーカイブからsourceTextを取り出す | 元UTF-8バイト列とSHA256が一致する |
| M04 | 旧IDの衝突と曖昧な色参照 | 既知参照だけ変換し、曖昧なものを報告する |

上表の39条件はDraft 1から維持する。追加30条件D201〜D230と20条件R101〜R120は[統合した適合条件表](FORMAT_SPECIFICATION.ja.md)を参照する。合わせて89観点であり、実行ケース数や合格数ではない。原型置換、既存ID保持、Core歌詞参照、保護、リンク解除、入力・出力の原子性を含む。過去のDraft 2 Revision 1実行記録は以前の配布物に残し、この正式配布セットには試験runnerと実行レポートを同梱しない。

任意仕様の編集動作は、その動作への対応を表明する実装だけに要求する。保護対象を守る再生成の試験には、操作前後のデータを用いる。外部アプリ間の読み書き試験は、仕様自身の検証と分けて記録する。

# 14 対応表明と実装順序

「OSTF対応」の一語だけで済ませず、Core読み込み、Core書き出し、未知情報を保持する編集、任意仕様ごとの表示・編集、独自の再生成を区別して示す。

## 初回リリースの範囲

初回3.0は、本仕様の正式Core、既存6名前空間の暫定profile `1.0.0-draft.1`、現在の明示編集と保守的移行を範囲とする。任意profileや編集操作は、それらへの対応を表明する実装だけに要求する。和声追従、部分override、時間伸縮、全曲シーン、記譜モデル、追加表情、外部アダプターは後続拡張とし、初回公開の完了条件には含めない。

現在の型・必須項目・数値上限を初回の基準とする。後続の変更で従来の有効文書や意味に互換性がなくなる場合は、その影響を示し適切な版を更新する。配布対象と利用条件は[LICENSE.md](../LICENSE.md)で明記し、2.3原典の出典と変更範囲は[出典一覧](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0)に記録する。

## 実装と検証の単位

以下は実装者に対応範囲を示すための整理である。本改訂の公開判断では追加の動作検証を要求せず、以前の配布物の試験結果を今回の合格実績として扱わない。

1. 厳密JSON入力、Core JSON Schema、時間・ID・参照の意味検証を実装する。

2. Coreから確定イベント列を取り出す参照実装と、独立した期待値による試験を用意する。

3. 相対フレーズ、生成文脈、表示と原典アーカイブの既存版を維持し、root版ごとに任意仕様の対応範囲を分ける。

4. 保守的な2.3変換と、明示的なサンプル規約解釈を分ける。

5. 再利用フレーズとクリップ候補の構造・意味検証、明示的な実体化・候補選択、操作前後の保護比較を独立した期待値で確認する。

6. Revision 1の原型置換・全instance更新で既存の発生IDを保持し、歌詞参照と保護を検査する。リンク解除ではCoreと元statusを保ち、すべての失敗が部分結果を残さないことを確認する。

7. 3つの対応root間の明示変換でCoreを保つ契約と、旧2版専用APIの互換性を区別する。配布物では仕様・Schema・記述例・過去の実行証拠の対象版を明示する。今回の未実行な変更を過去の合格結果に含めない。

検証結果には、実行したコマンド、実行環境、検証対象の版、正例と負例の結果、未対応の範囲を記録する。未実行の試験を合格数に含めない。

## 後続拡張と維持方針

| 項目 | 初回リリースの扱い | 後続で扱うこと |
| --- | --- | --- |
| 元フレーズと配置インスタンス | 既存の具体ノート・移調・反復に、明示的な原型全体置換・全instance更新とリンク解除を追加 | 自動再同期、部分override、時間伸縮、反復位相の拡張 |
| 候補と全曲シーン | Core clip集合の複数パート候補とenabled反映を定義 | 和声・歌詞・構成・routingを含む全曲状態、リアルタイムlaunch |
| 保護対象の検証 | 固定したbefore/afterのID・音高/対象・絶対tick・ゲート・Velocityの有無と値・有効状態を比較 | 実生成エンジンとの連携、秒単位や音色までの保護 |
| 入力音の順位選択 | 現在のrelativeには含めない | 音高順、入力順などを和声度数と区別する定義 |
| 記譜と演奏の対応 | Coreのゲートと歌詞割り付けを定義 | 音価、タイ、声部、異名同音、連符の保持 |
| 打楽器の詳細 | スロットID、役割、出力ヒント | 音源別マップ、チョーク、奏法 |
| 表情情報 | 3.1 Coreの演奏ノートに任意Velocity（1～127）だけを追加 | CC、Bend、AT等の独立イベントは対象外 |
| 複数の和声文脈の表示 | ID指定と重なりを許可 | レーン・優先表示・局所文脈の整理 |
| 数値上限 | 本仕様の上限を初回の基準として採用 | 互換性への影響を明示した将来の版で見直す |
| 外部製品との互換性 | 対応を仮定しない | 実際の入出力アダプターと相互運用試験 |

本策定の目的は、単独の小さなジェネレーターでも確定ノートの書き出しから参加でき、高度な実装が同じ基盤に作曲上の関係を追加できる状態を作ることである。

# 15 設計判断の根拠

以下の資料は、異なる作曲方法を想定するための根拠である。各製品がOSTFを実装しているという意味ではない。

| 設計判断 | 参照資料 |
| --- | --- |
| 2.3内部の未規範化データを推測で変換しない | [OSTF 2.3 Format Specification](https://github.com/yoh-nak/Open-Song-Timeline-Format-2.3/blob/90fce246de28a1b265fb6b25cc2866c70036fda6/docs/FORMAT_SPECIFICATION.md) |
| 相対フレーズと確定ノートを区別する | [RapidComposer Working with Phrases](https://musicdevelopments.com/workingwithphrasest.html) |
| 区間の前後に伸びる演奏を保持する | [Synfire Figure](https://docs.cognitone.com/synfire/G3/EN/parameters/Figure.html) |
| 再生成時の保護対象を記録する | [Captain Melody Guide](https://mixedinkey.com/captain-epic-tutorials/captain-melody-epic-guide/) |
| 入力ノートの順位と和声の度数を区別する | [Phrasebox 2 Manual](https://venomode.com/storage/bin/plugins/phrasebox-2/2.5.1/Phrasebox%202%20Manual%202.5.1.pdf) |
| コード周期と伴奏周期を分ける | [ChordPulse Play Menu](https://www.chordpulse.com/guide/play-menu.html) |
| GMを打楽器の唯一の表現にしない | [iReal Pro MIDI and MusicXML Export](https://www.irealpro.com/learn/exporting-to-midi-and-musicxml/) |
| ゲートと記譜上の音価を分ける | [Dorico Played and Notated Durations](https://www.steinberg.help/r/dorico-se/6.2/en/dorico/topics/key_editor/key_editor_notes_duration_played_notated_c.html) |
| メロディー先行の作曲も受け入れる | [Hookpad Aria](https://www.hooktheory.com/hookpad/aria) |
| 複数の入力を持つ生成も受け入れる | [Magenta Studio](https://magenta.withgoogle.com/studio) |

# 16 変更記録

Draft 1では、演奏の正本をtracksに統一し、音高または打楽器対象・開始位置・ゲートを定義した。PPQ宣言、境界を越える音、同音重複、安定ID、構造化和声、歌詞の多対多対応を定義した。拡張の状態と依存関係、および三つの標準任意仕様を定め、2.3原典保持と保守的な移行を組み込んだ。

Draft 2ではCore演奏の意味を維持し、phrase-libraryとclip-choicesを追加した。具体ノートtemplateからの有限反復・移調とCore対応、複数パートの候補をclip.enabledへ反映する契約、明示的な実体化と候補選択、固定した操作前後データによる保護比較を定めた。root版によるSchema/profile dispatchと明示的なDraft間変換を設け、既存profileの版を変更せず、2.3移行の出力先だけをDraft 2へ進めた。自動再生成、全曲シーン、音声/MIDIエンジンの実行は追加していない。

Draft 2 Revision 1ではwire版とSchemaを変えず、`replace_phrase_template`と`detach_phrase_instances`を追加した。前者はcurrentな原型と全instanceを一括更新し、存続する発生の既存Core IDを保ち、削除される音へのCore歌詞参照と指定された保護対象を検査する。後者はリンクだけを除去し、Core内容・歌詞・ID・ミュート・配列順と元のphrase statusを保持する。両操作とも入力を変更せず、失敗時に部分結果を出さない。自動同期、時間伸縮、和声追従、override層は追加していない。

Draft 2 Revision 2では、初回3.0の機能範囲と後続拡張を分け、公開草案としての可否と正式版への残件を明記した。既知profileの整数にも厳密な整数トークン規則を適用すること、同一候補選択で無関係な拡張を無効化しないことの表現を日英で揃えた。コード、Schema、サンプル、保存済みの試験結果は変更せず、追加の動作検証は行っていない。

3.0.0ではCoreの正式rootと対応Schemaを追加し、旧2つのSchemaをそのまま保持する。6つの暫定profileの版・意味と既存編集操作を維持し、Draft 2と3.0.0の両方で操作を扱う。`convert_version`は3root間を扱い、旧`convert_draft_version`とupgrade/downgradeは旧2版専用のまま保持する。2.3移行とSchema選択の既定を3.0.0とする。正式配布セットでは60件の3.0.0サンプルと2件の2.3移行元を整理し、全網羅マニュアル・リファレンス・対応表・ライセンス通知を同梱する。Core・Schema・参照コードの意味はこの資料整備で変更せず、動作検証は行っていない。利用条件は[LICENSE.md](../LICENSE.md)に記載する。

以後の変更では、Core、任意仕様、移行、検証のどこに影響するかを明記し、互換性を保てない場合は対象の版を更新する。


# 17 OSTF 3.1.0：トラック解釈・表示・MIDI変換の正式追加仕様（規範）

本章はroot `version: "3.1.0"`にのみ適用し、3.0.0の第2・6・7章で明示的に追加・変更した箇所より優先する。
既存の和声、歌詞、clip、note、6種類の暫定profile（正確な版`1.0.0-draft.1`）、整数tick、ID規則、明示編集操作は変更しない。
構造上の必須・任意・制約は[3.1.0 Schema](../schema/ostf-3.1.0.schema.json)で定義し、本章で意味規則を定義する。
「必須／禁止」は適合要件であり、「推奨」は実装上の勧告とする。

## 17.1 トラック種別：`tracks[].trackType`（任意）

| 値 | 許容されるノート対象 | 解釈 |
| --- | --- | --- |
| `melodic` | `pitch`のみ | 有音高楽器。調律済み808ベースや音高付き打楽器も含む |
| `drum` | `soundSlotId`のみ | 各種打楽器の意味的な発音スロット |
| `mixed` | `pitch`と`soundSlotId`の両方を許可 | 有音高と打楽器の混在。片方だけ、空でも可 |

明示的な`trackType`は、無効化されたtrack/clipを含む**保存された全ノート**に一致しなければならない。
空トラックには用途を先に指定してよい。`trackType`が省略されたら、全クリップの`pitch`のみ=melodic、
`soundSlotId`のみ=drum、両方=mixed、ノートなし=unknownと解釈する。
名前・role・instrument・MIDI番号・プラグイン名・MIDIチャンネルからは推定してはならない。
推定結果を元ファイルへ勝手に書き込まない。3.0由来の`pitch: 36`をGMドラムと自動解釈してはならない。

## 17.2 表示：`tracks[].preferredView`（任意）

値は`auto`/`pianoRoll`/`drumGrid`。省略時は`auto`として扱うが、属性を自動追記する意味ではない。
`auto`の場合、drumは`drumGrid`、melodic・mixed・unknownは`pianoRoll`を推奨する。
表示設定は**演奏に影響しない任意の希望**であり、ホストがUIを勝手に切り替える命令ではない。
混在トラックでdrumGridを指定した場合、有音高ノートが欠落する表示・変換を禁止する。
複合表示または安全なpianoRollへのフォールバックを行う。編集ソフトが対応しない表示は
ホストの安全な表示に戻してよいが、noteの`pitch`/`soundSlotId`は変更してはならない。

## 17.3 GMドラム：`soundSlots[].midiMapping`（任意）

`midiMapping`は`{"standard":"gm1","note":36}`などのオブジェクトで、`standard`と`note`の二つが必須。
`gm1`はGM Level 1打楽器キーマップのMIDIノート**35～81**のみ許容し、`custom`は0～127を許容する。
GM1の打楽器は通常MIDI **チャンネル10**（人が数える1始まり、MIDIデータ内部では9）を使う。
GM2、GS、XG、チョーク、アーティキュレーション、メーカー固有のキーマップはこのGM1宣言から推測しない。

`Sound Slot ID`は音楽上の発音対象の同一性を表す。`midiMapping.note`は外部MIDI出力上のキーであり、IDではない。
異なるslotが同一noteへ対応してもOSTFとしては有効だが、通常のMIDIだけに書き出すとIDを復元できないため
情報損失の報告が必要である。slotの`name`/`role`から音色や番号を自動推測してはならない。

3.0の`midiNote`は3.1でも**旧来のヒント**のままとする。
`midiNote`と`midiMapping.note`を同時に記載した場合は番号一致が必須（不一致は不正）。
`midiNote`のみのslotは、正式なGM1対応であると確定させず、MIDI出力未解決とする。
利用者が明示的に旧ヒント採用を選択した場合のみ番号を利用してよいが、その仮定を警告・変換記録に残す。
どの番号も未指定ならslotが消えるのではなく、割当を求めるか未解決として表示・保存する。

## 17.4 楽器名と音源の区別：`tracks[].soundSource`（任意）

従来の`instrument`（`name`と任意の`family`）は**作曲上の楽器の説明**であって、音色の再現や音源の実体を意味しない。
新しい`soundSource`は**再生に希望する音源の割当先**を示すヒントに限定する。
必須の`kind`は次のいずれか。

| kind | 意味 | 許される補助情報 |
| --- | --- | --- |
| `unassigned` | 未割当 | なし |
| `gm1` | GM1に対応する音源分類の希望 | 音色番号は規定しない |
| `externalMidi` | 外部MIDI先 | 不透明な`identifier`（任意） |
| `plugin` | ソフト音源プラグイン | 不透明な`identifier`（任意） |
| `hostManaged` | DAW内部の音源任せ | 不透明な`identifier`（任意） |

`gm1`と`unassigned`に`identifier`は書かない。**3.1では`program`自体を定義しない。**
`plugin`のidentifierはプラグインの識別情報に過ぎず、バイナリのプリセット・エフェクト・音源内パラメーター・権利を再現する契約ではない。
ホスト固有の状態は`extensions`の独自namespaceで不透明データとして保存してよいが、
非対応ソフトは解釈せず保持し、勝手に外部データを取得・実行・インストールしてはならない。
音源が存在しない場合は未解決と報告し、音色が完全再現できたと主張してはならない。

## 17.5 DAW共通のMIDI展開

変換の正本は`tracks[].clips[].notes[]`だけである。コード名、候補、フレーズtemplateからノートを勝手に再生成してはならない。
絶対発音時刻は`clip.start + note.start`、終了時刻はそれに`duration`を加えたtick。
クリップ境界の前から始まる弱起、境界を越えるテール、同音重複、無効clipも正しく保持する。
ホストが保持できない場合、欠落を隠さず情報損失を報告する。

1 OSTF trackは1つの論理的な出力グループに対応する。有音高ならMIDIキーは`pitch`の値そのまま。
ドラムなら`midiMapping.note`を用いる。番号未解決のslotを勝手にGMへ置換しない。
`mixed`は必要なら有音高用と打楽器用の2ストリームに分割し、双方の出自`trackId`/`clipId`/`noteId`を保持する。
元のノートIDをMIDIメッセージだけで完全復元することは一般にできないため、可能ならDAWのメタデータやsidecarで対応表を保存する。
GM1打楽器では通常チャンネル10を提案するが、Liveなどのルーティングを仕様が強制しない。

3.1 Coreのノートに明示されたVelocityはMIDIのNote Onにそのまま利用する。省略されているノートに限り、アダプター側で公開した既定値（例：100）を使い、元のOSTFには書き戻さない。CC、Pitch Bend、Aftertouch、Note-off VelocityはCoreに含まれない。
PPQ変換では絶対開始・終了時刻を正確に保存できる限り保存する。正確に表現できない場合は
デフォルト拒否、明示的な丸め選択、ノート単位の誤差報告を必須とする。ゲート0への丸めは拒否する。
SMFのメトリックPPQは15bitであり、OSTFのPPQ上限960000をそのままSMFに載せられるとは限らない。

## 17.6 版とリリース境界

3.1 Readerは正しい3.0.0ファイルを**バージョンを書き換えず**読み込めなければならない。
明示的な3.0→3.1移行はCoreの音楽情報を変更しない。未指定のGMマップや音源を捏造しない。
3.1→3.0で3.1専用属性を消す場合は損失を明示して利用者が許可する必要がある。
3.0 Readerが3.1をそのまま受理する保証はしない。

形式仕様・Schemaの公開、実装コードの検証、DAW間の動作検証は**異なる段階**である。
動作未検証でも仕様資料を正式公開することは可能。ただし、Live/Cubase/Logic/REAPER等で
インポート済み・音色完全一致・全ソフト互換性を確認済みと主張してはならない。
マッピング未解決やプラグイン不在があっても、曲のOSTF情報として有効である。


## 17.7 多言語名称（`localizedNames`、`localizedTitles`）

**3.0から必須である`tracks[].name`は、3.1でも元のトラック名として必ず保存する。**
UTF-8 JSONなので日本語・英語・中国語・韓国語・アラビア語などを利用でき、複数言語を同じ文字列に混ぜてもよい。
IDは名前と独立したASCII識別子であり、表示言語を変えても変更しない。

3.1では`track`、`instrument`、`soundSlot`、`clip`、`section`に任意の
`localizedNames`を追加する。`song`には既存の`title`を残し、任意の
`localizedTitles`を追加する。辞書のキーはBCP 47の限定した構文を持つ言語タグ
（`ja`、`en`、`en-US`、`zh-Hans`、`zh-Hant-TW`、`ko`、`ar`、`sr-Latn-RS`等）、
値は空でないUnicode文字列とする。タグの大文字・小文字を区別せず、
`ja`と`JA`のような実質重複を禁止する。古い特殊タグやprivate-useのみのタグは
このCoreの対象外とし、必要なら版付き拡張で扱う。辞書自体が空なら省略する。

表示時は、**言語タグ完全一致 → 末尾サブタグを1つずつ落として検索 → 元の`name`または`title`**
の順で選ぶ。例えば`zh-Hant-TW`の希望では`zh-Hant-TW`、`zh-Hant`、`zh`を探す。
`zh-Hans`への勝手な切替や機械翻訳はしない。元の文字列、アクセント、Unicode正規化形式を
保存中に無断で変えない。UIの言語切替は演奏データ・音源割当・ID・元の名称に影響しない。

他DAWに取り込む際は、既定で元の`name`とトラック配列順を復元し、
別言語表示は利用者が選択した場合に用いることを推奨する。
多言語名称をホストに保持できなければ、無損失の往復変換を主張せず
`LOCALIZED_NAMES_LOST`等で報告する。普通のMIDIファイル単体では
多言語名称とOSTFの元IDをすべて保持する保証はない。
双方向文字・絵文字・異なるフォントを安全に表示し、見切れはスクロールや全名称の確認で対応する。
名前は実行可能なHTML・設定コマンド・楽器判定条件として扱わない。

```json
{"id":"trk-taiko","name":"和太鼓","localizedNames":{"en":"Taiko Drums","ko":"다이코"},"trackType":"drum","drumKitId":"kit-taiko","clips":[]}
```

## 17.8 ドラム・パーカッション複数トラックとキット管理

`tracks[]`の本数は固定せず、ドラム／ラテンパーカッション／電子打楽器／メロディー等を
自由に構成できる。Drum Kitの名前でDAWの音源を自動ロードするものではない。
全体の`soundSlots[]`は固定数ではないグローバルな発音対象であり、
未使用のSound Slotも保持できる。3.1で追加する任意の`drumKits[]`は
複数のキット定義を保存する。各キットは`id`、元の`name`、任意の
`localizedNames`、順序付き・重複なしの`soundSlotIds[]`を持つ。
`soundSlotIds[]`に含まれるIDは既存のグローバルSound Slotを指す。
空のキットも有効で、同じSound Slotは複数のキットから参照可能。
キットのIDもCore全体で一意とする。3.1の型付きCore参照`ref.type`には`drumKit`を追加し、任意の拡張情報がキットへの依存を宣言できるようにする。3.0のenumは変更しない。

`track.drumKitId`は任意で、参照するキットのIDを示す。
明示的な`melodic`トラックまたはピッチノートしかないトラックには設定しない。
該当トラックが保存している全打楽器イベント（無効化されたクリップを含む）の
`soundSlotId`は、参照キットの`soundSlotIds[]`に存在しなければならない。
`mixed`トラックのピッチノートはキットの構成員ではない。
キットのないトラックも有効で、その場合は従来の`soundSlots[]`の配列順を
ドラム行の並び順に利用する。キットを利用したかどうかで演奏ノートを変えない。

Drum Gridの通常表示では**そのトラックの保存ノートに参照される行だけ**を表示し、
表示行はキットの`soundSlotIds[]`順（キットなしは`soundSlots[]`順）とする。
無効化されたノートの行も編集可能性のため使用中として扱う。
行が増えたらスクロールして表示し、任意の「未使用も表示」で定義済みの全Slotを表示できるよう推奨する。
表示から隠しても元データから削除してはいけない。

トラックやキットの削除、キットの付替、Sound Slotの削除は明示操作とする。
参照中のSlotやKitを削除して参照先が失われた文書は無効とし、同名／同じMIDI番号の
別Slotへ勝手に振り替えない。同じ音を別々のドラムキットで異なるMIDI番号へ割り当てる必要がある場合は、
別のSound Slot IDまたはDAWアダプター側の明示設定を使う。
キット内のMIDI割当上書きや音源プリセットの復元は3.1で標準化しない。

## 17.9 音色情報とProgram Changeを標準化しない方針

OSTF 3.1は簡易アレンジ交換を目的とし、DAWのプロジェクト保存形式ではない。
トラック名・順序・役割・楽器の説明・種類・Sound Slot・任意のキット構成により、
音源に依存しない論理的な編成を復元する。
既存の`instrument`は音楽的な説明であり、任意の`soundSource`は
再生先の希望を示す不活性なヒントである。保存済み音源の状態ではない。
`kind=gm1`も音源の分類を示すだけで、音色のProgram番号は持たない。

**Core 3.1にはProgram Change、Bank Select（CC0/32）、GM音色番号、
プラグインプリセット・音源パラメーター・サンプル音声・CC・Bend・ATは設けない。ノートの任意Velocityは音源の状態復元を意味しない。**
DAW取り込み時にProgram Changeやプリセット切替を勝手に送らない。
音源が必要な場合はDAW側・利用者側で選択する。発音の同一性は保証しない。
ホスト側の制約で多言語情報、キット構成、IDが失われる場合は、
メタデータ保存先を設けるか損失として報告する。

## 17.10 3.0後方互換・公開の境界

3.0から必須だったCore項目、音楽ノート・コード・歌詞・クリップ・IDの意味は維持する。
追加する3.1情報はすべて任意とする。3.0ファイルを3.1 Readerが読む場合は
元のroot版を勝手に変更せず、翻訳名やキットを自動で発明しない。
3.1から3.0へのダウングレードでは、トラック／楽器／セクション／クリップ／Slot／
曲名の翻訳情報とキット参照、キット一覧、ノートの`velocity`の削除をすべて明示的な損失として報告する。
6つの任意profileはすべて元の`1.0.0-draft.1`とし、再生や編集を自動実行しない。

本仕様・Schema・サンプルは静的な策定成果物である。
動作検証、DAWへの貼付、外部プラグイン動作、相互運用の成功は主張しない。


## 17.11 演奏ノートだけの任意Velocity（3.1規範仕様）

OSTFは**作曲支援ソフト間の楽曲データ交換**を目的とし、DAWの音源状態や
細密な演奏制御を再現する形式ではない。`velocity`を追加できるのは
**`tracks[].clips[].notes[]`内の実際に演奏するCoreノート**だけである。
`velocity`は省略可能、指定時はJSON整数トークンで**1～127**とする。
0、128、負数、`null`、`true`、`1.0`、`1e0`は認めない。
省略と明示値100は別の意味であり、Schemaの`default`を設けない。
**保存時に省略値を勝手に100として補完してはならない。**

この1つの任意属性は主旋律、通常楽器、ドラム、`mixed`の全演奏ノートに共通する。
主旋律は従来の`primaryMelodyTrackId`で指定する1本のままであり、
ハモリ・バックコーラスは必要に応じ通常のアレンジトラックに確定済みノートとして保存する。
和声の`harmony.chords`、コード構成音・相対ノート定義（Note Profile）、
歌詞、発音表記にはVelocityを付けない。コードから音符や強弱を自動生成する指定でもない。

- ノート、クリップの移動・分割・複製・再保存時には、残す演奏ノートの明示Velocityを保持する。
  無効化したトラック／クリップのノートも勝手に変更しない。
- MIDI 1のNote Onに変換する場合は、明示値を**そのまま**使う。
  省略ノートに限り、アダプターが宣言した既定値（参照例は100）を出力時だけ使用し、
  **元のOSTFに書き戻さない**。明示値と補完値の由来を出力・損失報告で区別する。
- Velocityを取得できない輸入経路では値を推測して原本として保存せず、省略と損失報告を行う。
  Velocityだけでサンプルレイヤー・音色・奏法の再現性を保証しない。
- CC、Pitch Bend、Aftertouch、MPE、Note Off Velocity、Program Change、
  音源プリセット・オーディオ情報は3.1 Coreに追加しない。
- 3.0を3.1で開いてもroot版や省略Velocityは変えない。
  3.0→3.1の明示移行でもVelocityを発明しない。
  3.1→3.0の際はVelocityの削除を一覧に記録し、明示的な損失許可がなければ拒否する。
  許可時もノートのID、音高、開始位置、長さ、Sound Slot参照は維持する。

**既存の任意フレーズprofileの版は変更しない。**
`org.ostf.phrase-library.v1`の正確な版`1.0.0-draft.1`にある
`phraseTemplate.notes[]`は旧型の原型ノート（`phraseSourceNote`）を用い、
Velocityは許可しない。生成済みCoreノート側には独立のVelocityを保存できる。
`current`のリンク一致判定は従来どおり音高・時刻・ゲート・ID等で行い、
Velocityは旧profileの対応比較対象には含めない。
リンク済み原型の明示更新でCoreノートIDが存続する場合、そのVelocityを保持する。
新規発生ノートはVelocity未指定となる。原型自体にVelocityが必要なら
将来、任意profileを**別版として**定義する（3.1の必須範囲には追加しない）。

```json
{"id":"snare-hit","start":960,"duration":120,"soundSlotId":"snare","velocity":41}
```

[サンプル74](../samples/74_velocity_melody_optional.ostf)はメインメロディー、
[75](../samples/75_velocity_drum_accents.ostf)は打楽器、
[76](../samples/76_velocity_phrase_instances.ostf)は既存フレーズprofileと共存する例。
いずれも記述例であり、実行・音声再生・DAWでの確認結果ではない。

# 18 OSTF 3.2.0 正式追補（3.1と矛盾する記述に優先）

**形式版は`3.2.0`とする。** 3.1の必須root11項目・時間単位・演奏ノート・歌詞・相対ノート・6つの任意profile（それぞれ`1.0.0-draft.1`）は維持する。追加される`contributors`は任意であり、3.0/3.1のファイルを読んだだけで3.2へ書き換えてはならない。構造定義は[3.2 Schema](../schema/ostf-3.2.0.schema.json)を優先する。

## 18.1 コード・ボイシングとVelocity

`harmony.chords[]`へ任意の`voicing`を追加する。`voicing.pitches`は必須の非空配列で、各値は整数0～127の絶対音高。中音C=60。値の重複を認め、元の配列順も保持する。1.3の`voicing.pitches`と同じスナップショット思想を3.2 Coreに明示する。

任意の`voicing.velocities`は、`pitches`と**要素数および位置が完全に一致**する配列。各値はJSON整数トークン1～127、または未指定を表す`null`。`null`はVelocity 0やNote Offではない。`velocities`配列自体の省略も可能。`1.0`・`1e0`・`true`は整数Velocityとして不正。重複音高があっても順序だけで対応付けるため、編集時には対応するVelocityを同時に移動する。音高を基準に勝手に並べ替えたり、同じ音高のVelocityを統合したりしてはならない。

ボイシングは**コードに紐付く任意の表示・試聴候補情報**であり、開始時刻・各音の長さ・音色・トラックを持つ演奏イベントではない。コードの`start`と`duration`は和声の区間を表すだけで、各ボイスのGateを決めない。自動的に演奏ノートを生成したり、既存トラックと重ねて二重再生してはならない。試聴・DAW貼付・伴奏生成は各ソフトウェアの明示操作で行う。その際のGateや未指定Velocityの補完方法もそのソフトが選ぶ。コードの`root`・`intervals`・`bass`は従来どおり和声情報であり、`intervals`自体にVelocityを追加しない。

既存の`tracks[].clips[].notes[].velocity`は変更しない。メインメロディー、通常楽器、ドラム、Mixedの実ノートは**3.1からすでに任意Velocity 1～127に対応**している。ボイシング内のVelocityと実演奏ノートのVelocityは独立し、自動同期を要求しない。フレーズ原型profileのノート構造は旧版固定のまま変更しない。和声との不一致を警告することはできるが、ボイシングを勝手に更新しない。

**書式例**：

```json
"voicing": {"pitches": [48,55,59,64], "velocities": [90,72,null,105]}
```

## 18.2 作曲者・作詞者などのクレジット

rootの任意`contributors[]`は、人物・団体の同一性を楽曲内で管理する。要素は必須`id`（Core全体で一意）と非空の原文`name`、任意の`kind`（`person`/`organization`/`other`）、従来の`localizedNames`を持てる。同名でも別人物の可能性があり、翻訳名の違いも別人とは限らない。外部DBで身元が確認されることまでは保証しない。

`song.credits[]`は任意の**順序付き**クレジット配列。要素には必須の`id`、`contributorId`（`contributors[]`のID参照）、`role`（`composer`/`lyricist`/`arranger`/`performer`/`producer`などの小文字の意味識別子。固定列挙ではない）、任意の原文表示`label`を記録する。同じ人物を複数役割に割り当てられる。表示順は配列順。参照先がないクレジットは不正。`contributor`・`credit`・追加する`song.alternateTitles[]`の`id`は既存Core IDと衝突してはならない。

曲情報として任意の`song.subtitle`、`song.description`、`song.alternateTitles[]`（各要素に`id`・`title`）を追加する。これらはUTF-8文字列で、日本語・英語など複数言語が混在してもよい。

## 18.3 共通Localization Extension

固定言語一覧を用意しない。任意のBCP 47言語タグ（`ja`、`en`、`zh-Hant`、`pt-BR`等）から翻訳文字列へ対応付ける。言語タグは大文字小文字を区別せず、同じ対象・項目・言語について重複定義してはならない。Schemaはタグの一部の構文だけを静的検査する。厳密なBCP 47適合性・標準化は意味検査で判断し、Schemaだけで完全適合を保証しない。

`extensions["org.ostf.localization.v1"]` は任意で、正確な版は`1.0.0`。従来の拡張envelopeの`version`、`status`、`dependencies`、`data`を維持する。`data.entries[]`は`{target,field,values}`。`target.kind`と安定した`target.id`で参照する。例外として曲全体を示す`{ "kind": "song" }`だけはID不要。任意profile内部の名前を翻訳するときには、`target.profile`で対象namespaceを示す。配列番号や変更可能な表示名を識別子として使わない。既存3.1の参照型にない種類も扱うため、`dependencies.scope:"document"`が推奨。

| target.kind | 翻訳可能なfield | 注意 |
|---|---|---|
| `song` | `title`、`subtitle`、`description` | 元の項目を必ず残す |
| `alternateTitle` | `title` | 固有IDで区別する |
| `contributor` | `name` | 名前が変わっても同一人物IDは変わらない |
| `credit` | `label` | `role`は言語非依存の意味識別子 |
| `section` | `name` | セクションIDを参照 |
| `track` | `name`、`instrument.name` | `instrument`はトラック内の既存楽器情報 |
| `clip` | `name` | クリップ固有ID |
| `soundSlot` / `drumKit` | `name` | 音色やマッピングは翻訳しない |
| `lyricDocument` | `title` | 歌詞文書の題名のみ |
| `resource` | `name` | 素材参照URIは翻訳しない |
| `phraseTemplate` | `name` | phrase-library profileの局所ID |
| `clipCandidate` / `clipChoiceGroup` | `name` | clip-choices profileの局所ID |
| `roleDefinition` | `name` | appearance profileの局所ID |

`current`拡張の翻訳先には原文フィールドが存在しなければならない。翻訳だけで未定義のCore文字列項目を作り出せない。翻訳のない言語は原文へ戻す。表示側では希望言語の完全一致、上位言語の代替、原文の順を推奨するが、OSTF Playerに切替UIを要求しない。自動翻訳・自動転写・歌唱言語変更・トラック演奏変更は行わない。

3.1の`localizedNames`と`localizedTitles`は**廃止せず**読み込めるままにする。共通拡張と同じ対象・言語の翻訳が重複する場合は、文字列が完全一致していなければ不正とする。新しい作成ソフトは共通拡張に集約し、同じ翻訳を旧フィールドへ重複保存しないことを推奨。翻訳を編集してもCoreのID・コード・音符・歌詞割当を勝手に変えない。未知・未対応の拡張を保存するときに値を欠落させない。

**歌詞本文の翻訳は対象外。** 訳詞は音節・発音・ノート対応が異なるため、別の歌詞文書や専用ソフトで管理する。歌詞の訳文を曲名と同じ文字列差し替えとして扱わない。


**Schemaの対象ID制約：** `localizationTarget`では`kind: "song"`以外に`id`が必須で、songへの参照では`id`・`profile`を付けない。profile内の要素は該当namespaceの`profile`指定も必須とする。これは表示UIの慣習ではなく、Schemaが規定する制約である。

## 18.4 旧版互換と損失報告

3.2対応の読み手は、3.0・3.1文書を元の`version`のまま読み込む。新しいフィールドを捏造しない。3.1→3.2の明示変換では従来の内容を保ち、必要ならroot版だけを更新できる。3.2→3.1ではボイシング、クレジット、翻訳profile、サブタイトル等の3.2専用情報が失われるため、削除項目の明示とユーザー承認を必要とする。3.0へ戻す場合には3.1独自情報（実ノートVelocity等）の損失も報告する。OSTF 1.3からの移行は文書構造が違うため、`version`だけの書換えでは成立しない。

`status:"current"`の翻訳対象が削除される編集では、未対応ソフトは根拠なく参照を付け替えてはならない。翻訳を保持したまま`stale`にする、編集を中止する、または損失を利用者へ明示する。意味参照が壊れた`current`拡張は不正。翻訳のない原文を自動で変えたり、データを勝手に並び替えてはならない。

## 18.5 非対象機能と公開判定の範囲

本形式は作曲支援ソフト間の音楽情報交換用であり、DAWプロジェクトではない。コードボイシングVelocityの追加はCC・ピッチベンド・AT・MPE・音源preset・Program Change・ミキサー・音声レンダリング・本格的な歌唱合成の標準化を意味しない。3.2のSchema・文書・サンプルの静的検査と、DAW/Max for Live上での動作検証は別物である。後者は実施したと表明しない。

## 18.6 Sound Slot単位の独立したドラム再生契約（3.2 規範・追加パラメーターなし）

本節は、OSTFファイルの**論理的な演奏イベント**と、各ソフトウェアの**実際の発音**との境界を定める。
これは音声サンプル、音源プログラム、打楽器シンセサイザー、DAWのドラムトラックをOSTF Coreに追加するものではない。再生機能のない読込ソフトもOSTFを利用できる。

1. `soundSlots[]`は文書内で一意な発音対象の登録簿である。各ノートの`soundSlotId`は**その登録簿のID**を指す。`role`、`name`、`localizedNames`、`midiNote`、`midiMapping.note`、ドラムキット内の行番号は同一性の代用品ではない。同じ役割／名前／MIDI番号の複数Slotを一つへ統合してはならない。異なる文書で同じIDが現れても、同じサンプル音色であると推測しない。
2. 保存された各Coreノートは独立したイベントである。ドラムトラックを何本に分けていても、別々の`soundSlotId`が同じtickに開始すること、同じSlotが重なって連打されること、複数トラックが同じSlotを参照することは有効であり、読込・変換時に重複を除去しない。同じSlot IDを複数トラックで共有する場合は**同じ論理打楽器**への参照であり、別の発音対象を区別したい場合には別のSlot IDを使用する。発音対象を持つ演奏トラックと無効なtrack/clipは既存Coreの`enabled`規則に従い、無効でも記録は保持する。
3. `drumKits[]`および`track.drumKitId`はSlotの**所属・表示順**を表す。キットごとの音源をロードしたり、トラック別の新しいMIDIノート番号を自動生成する命令ではない。ひとつのグローバルSlotは複数キットから参照してよい。Playerが音色を割り当ててもキットや元のノートを書き換えない。
4. `soundSlots[].midiMapping`は**外部MIDIの発音キーへの明示的な割当**である。`standard:"gm1"`かつ35～81ならGM1打楽器の対応表を利用してプレビュー音色を選ぶことができるが、正確な音響を保証しない。`standard:"custom"`の36番をGM1のKickとして扱ってはならない。旧`midiNote`だけからGM1宣言・音色・Slot種別を創作しない。自由文字列の`name`/`role`から自動的に音色の同一性を確定しない。
5. **Sound Slot別再生に対応するソフトウェア**は、Slot IDごとの発音割当を独立して管理すること。音源ボイスを内部で共用してもよいが、異なるイベントを1イベントへ結合してはならない。音色解決の推奨順は、(a)ユーザーが選択した当該文書／Slot用の割当、(b)Player側に保存した当該文書／Slot用の割当、(c)明示されたGM1マップに対するPlayer内蔵の近似音色、(d)未解決、である。未解決なら警告のうえ無音または明示した汎用プレビュー音を選択可能とし、元のOSTF情報は必ず残す。`soundSource.kind:"plugin"`が書かれていても、外部プラグインやサンプルの自動取得・起動はしない。
6. `note.start`と`note.duration`はCoreの**発音開始とノートゲート**であり、ドラムの録音波形が何秒鳴り続けるか、One-shotを何秒で切るかの共通契約ではない。One-shot音源の残響・リリース、ハイハットのチョーク、ラウンドロビン、フラム、ロール、ボイス数の制限・ボイススチールはソフト固有である。`velocity`が明示されていればノート単位に利用し、省略時はPlayer側で決めたプレビュー既定値を用いてよいが、その数値を原本へ補完しない。
7. 大量のSlotを持つ文書、同時発音、同一Slotの高速連打を受けても、仕様上のSlot数・ノート数の上限や同時発音禁止ルールを新設しない。実装固有の負荷・発音数制限がある場合は、表示・警告できることを推奨し、再生上の省略をフォーマット上のノート欠落として保存しない。

**適合範囲：** OSTF Readerに必須なのはSlot IDとイベントの保存・参照解決である。全47種類のGM1打楽器音の内蔵、音色の一致、Player単体での音声再生はフォーマット適合の必須条件ではない。「Sound Slot別に発音可能」と主張するPlayerは上記のイベント独立性を尊重し、近似音・未割当を利用者へ区別して示す。

## 18.7 DAW・ドラムプラグインへの可逆性境界（3.2 規範・追加パラメーターなし）

- **OSTF対応ソフト同士**は、`trackId`、`clipId`、`noteId`、`soundSlotId`、時間、明示Velocity、Slot/Kitsの構成を直接交換できる。ドラムキットを展開して単一のMIDIノート番号へ変換する必要はない。
- **一般的なMIDI1/SMF出力**では`midiMapping.standard`と`note`の対を参照する。`gm1`はGM1向け、`custom`はユーザーが管理する別マップ向けであり、番号が同じでも互換音色とはみなさない。出力先固有の受信キーへ再マップするときはアダプター／利用者の明示設定で行い、元の`soundSlotId`と`midiMapping`は変更しない。GM1の一般的なチャンネル10は参考ルーティングであり、CoreへのMIDIチャンネル固定を要求しない。
- **損失を報告するケース：** 異なるSlotが同一MIDIキー・チャンネル・ポートに合流する、出力先に対応するキー／パッド／音源がない、設定したPPQで正確にtickを表現できない、ノートの重複／テール／有効状態／キット情報を保存できない、など。とくに同じMIDI番号の2つのSlotはOSTFでは正当でも、通常の`.mid`ファイルだけから元のSlot IDを復元できない。可能な場合はSidecarなどで識別情報を併送する。情報損失を隠して「無損失」と報告しない。
- **未解決と不正を区別する：** 参照先Slotが文書に存在しないノートは不正だが、Slotは存在しMIDIマップがない文書は有効である。後者はPlayerのプレビュー音を割り当てることはできるが、外部MIDI出力ではユーザーの割当なしにGM番号を推測してはならない。
- 一般的なドラムフレーズ生成プラグインとの交換は、その製品が受け入れる**MIDI入力／出力**または個別のOSTFアダプターで行う。プラグイン内部の編集可能なパターン、Articulation、Choke、ベロシティレイヤー、確率、音源プリセットを完全に復元することまでは保証しない。

**3.2の拡張判断：** この契約は既存の`trackType`、`drumKits`、`soundSlotId`、`midiMapping`、`velocity`のみで表せるため、新規Coreフィールド・Program Change・CC・サンプル音源情報は追加しない。より詳細な打楽器音色分類・奏法・チョークの互換性が必要になった場合は、将来の**任意・版付き拡張**で扱う。参照用サンプル：[61 GM1](../samples/61_gm1_drum_grid.ostf)、[64 Custom](../samples/64_custom_drum_mapping.ostf)、[66 未解決](../samples/66_unresolved_sound_slot.ostf)、[71 複数キット](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf)、[73 未使用Slot](../samples/73_unused_sound_slots_preserved.ostf)。

## 18.8 Mixedトラック・同一音高の重複・MIDI出力先の分離（規範補足・新規フィールドなし）

**発音対象の種類はMIDI番号より優先する。** `mixed`トラックの`note.pitch:36`は音程を持つノートであり、`note.soundSlotId:"kick"`が参照するSlotのGM1対応番号が36でも、両者は同一の発音対象ではない。OSTFの正本は`pitch`か`soundSlotId`かの区別である。読込と再保存でこの区別を失ってはならない。

通常の音程ノートと打楽器ノートをMIDI1へ変換する場合、アダプターは必要に応じ**別々の出力先**（トラック・チャンネル・ポートなど）を明示設定して分離することが望ましい。単一のキー／チャンネル／ポートに統合すると楽器の意味や実際の音が変わり得るため、情報損失を必ず報告し、原本のノート種別を書き換えてはならない。一般的なGM打楽器用チャンネルは出力先の慣習であり、OSTFのチャンネル指定を意味しない。Mixedトラックに1つでもドラムがあるからといって、通常の音程ノートすべてをドラムチャンネルへ送ってはならない。分離できない場合には、出力を中止するか利用者が明示的に損失を許可する。

**同一音高・同一出力先で重なる複数ノート**もOSTFでは異なるNote IDを持ち、開始位置・ゲート・Velocityを別々に保存する。一方、一般的なMIDI1機器では同じ音高・チャンネルのNote Onが重なると、どのNote Offがどの発音ボイスを終了させるか曖昧になる場合がある。書出側はこれを黙って重複排除・短縮せず、必要なら出力先を分離するか、損失・解決ポリシーを明示する。無効なクリップ内のノートも保存したままにする。[63 Mixed](../samples/63_mixed_track.ostf)は音程36とGM1 Kick36を同時に保存した例、[69 Overlap](../samples/69_muted_and_overlapping.ostf)は同一音高の重複と無効クリップの保持例である。

**Sound Slot単位のMuteはCore情報ではない。** 3.2に存在する有効状態は`track.enabled`と`clip.enabled`である。PlayerがDrum Gridの行ごとにMute/Soloを設けても、それは原則としてPlayer内のプレビュー操作であり、Slot自体を無効化したり元ノートを削除したりしない。複数ソフト間で無効なパートを交換したい場合には、独立したトラック／クリップの有効状態を利用するか、利用者が明示的に確定した編集結果として保存する。

## 18.9 文書識別・主旋律参照・公開範囲（規範補足・新規フィールドなし）

OSTF Coreの`id`は**文書内で一意**であって、別ファイルでも同じSlot IDやTrack IDを使用できる。タイトル・翻訳名・ファイル名・Slot IDだけを使ってPlayer側のプレビュー音色割当を別文書へ流用すると誤発音の原因になる。Playerは、読み込んだ文書のインスタンス／ローカル識別とSound Slot IDの組を使って独自の音色割当を管理してよいが、その割当は**OSTFの音色情報ではない**。同名ファイルのコピーやリネームとの対応はPlayer側の判断とし、Coreに新しい文書IDや音源プリセットを要求しない。

`song.primaryMelodyTrackId`は既存の実トラックを**最大1本指定する**情報である。明示的な`drum`トラックやSound Slotノートだけのトラックを通常の音程メロディーとみなすべきではない。Writerはこのような参照を避け、Readerは矛盾の警告を出してよいが、元の参照を勝手に書換えたり、打楽器からメロディーを自動生成したりしない。Mixedトラックに実際の音程メロディーがあれば主旋律に指定してよい。3.0/3.1の適法な文書を遡及して不正にする新しい制約は追加しない。

この追補は**新規Coreフィールド0件、Schema受理条件変更なし**である。DAWに貼り付けずにPlayer自身が音を鳴らすことは実装で可能だが、音色品質・同時発音数・Note Off処理・音源プログラム・プラグイン連携は各ソフトウェアの責任である。仕様公開のGOと、Playerや他社ソフトとの動作・互換性の実証は別に判定する。

## 18.10 ボイシングの表示と実演奏ノートの分離（規範補足・新規フィールドなし）

**画面に表示した音高が、必ずしも演奏Note Eventとは限らない。** 3.2の`harmony.chords[].voicing`と`tracks[].clips[].notes[]`は意味の異なる独立データである。Playerは`voicing.pitches`と対応する`velocities`をコード画面やピアノロール上の**非演奏オーバーレイ**として表示してよいが、実ノートと識別可能にしなければならない。ボイシング各音には独自のNote ID・開始位置・ゲートがない。コードの`start`／`duration`を表示位置に使えても、それを各ボイシング音の演奏Note On／Off時刻だと断定してはならない。

**自動再生の正本は採用済みのCore実ノートだけ**である。ファイル読込、表示モード変更、コード選択をきっかけに、ボイシングから別の演奏クリップを暗黙に生成したり二重発音させたりしない。ユーザーがコード試聴を明示した場合は、ソフト側のゲート・Velocity既定値でプレビューしてよいが、未指定の`voicing.velocities`やNoteの`velocity`を原本へ勝手に補完しない。ユーザーがボイシングを明示的に演奏トラックへ確定した場合に限り、新しいNote ID・配置・ゲートを作成し、元コードは原則維持する。`null`や未指定Velocityの解釈は表示する。同音高・同時刻の既存ノートを自動で上書きしない。

メインメロディーは`song.primaryMelodyTrackId`で指定する**1本の通常演奏トラック**であり、ノートの二重保管場所ではない。Song／Track画面では同じTrack ID・Note IDから音高・ゲート・Velocityを読み取り、プレビュー音色、ドラムパッド、ミキサー状態、表示言語に依存させないことを推奨する。ボイシングだけがあり演奏トラックがなくても正当なファイルであり、コード音源を持たないソフトは発音せず表示するだけでもよい。OSTF Readerにピアノロール、内蔵音源、多言語切替、コード試聴、MIDI出力の実装義務はない。

**独自ドラム音色の互換性には意図的な限界がある。** Sound Slot ID、自由文字列`role`、`custom` MIDI番号だけでは、他社プラグインに共通の実音色を一意に復元できない。音声サンプル転送、標準化された音色ID、Velocity Layer、Choke、奏法情報はCore 3.2の対象外とする。打楽器名と明示GM1マップの意味が不一致に見える場合は警告してよいが、黙ってGM1番号を変更したり音源IDを作り出したりしない。制約や割当損失は18.6～18.9に従いソフト／アダプター側で報告する。
