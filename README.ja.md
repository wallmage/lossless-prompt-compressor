# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111927?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | 日本語 | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## なぜ作ったのか

システムプロンプトの中の全トークンは、毎回の API コールで払う税金だ。35,000 語の指示ファイルは約 48,000 トークンのコンテキストを意味する。モデルがユーザーメッセージを読む前に、ウィンドウの大部分がすでに埋まっている。1 日数千リクエストを掛け算すれば、フォーマット、冗長な記述、人間しか読まない文章にお金を燃やしていることになる。

これを解決するために作った。Lossless Prompt Compressor は AI 指示ドキュメント -- システムプロンプト、CLAUDE.md、エージェントルール、プロダクト仕様書、マスタープラン -- を受け取り、徹底的に圧縮する。要約ではない。圧縮だ。すべての事実、フィールド名、制約条件、相互参照がそのまま残る。削られるのはパッケージング：モデルが必要としない Markdown フォーマット、重複した記述、チュートリアル的な足場、モチベーション系の埋め草、そしてトークンを消費するだけで情報を運ばない文法的な接続詞。

典型的なプロダクト仕様書で、4 パスすべて実行すると 50-65% の削減。毎回の API コールで実際に節約できるお金であり、本当に重要な会話のために解放されるコンテキストウィンドウだ。

このコンプレッサーは [vibecheck](https://github.com/wallmage/vibecheck) の圧縮パスの中核でもある。

## 「ロスレス」の本当の意味

これは要約ではない。要約は何が重要かを判断して残りを捨てる。指示ファイルでそれをやるのは危険だ -- 制約が一つ抜けただけで、モデルが明示的に除外した機能をハルシネーションで生成しかねない。

ロスレスとは：元のドキュメントに「フリーティアの補助金を月間収益の 3% に制限」と書いてあれば、圧縮版もまったく同じことを言う。事実が落ちたり、曖昧に書き換えられたり、静かにマージされたりすることはない。セクション番号は固定されたままなので、「S8.7.3 参照」のような相互参照は決して壊れない。AI が圧縮版から生成するコードとアーキテクチャ判断は、原版から生成するものと同一になる。

この保証は承認ゲートによって担保される -- 機械的でないすべての編集が前後の差分とともに提案され、承認された場合のみ適用される。

## 4 パス、23 のテクニック

コンプレッサーは 4 つのパスを段階的に実行し、各パスでより深く踏み込む。どこで止めるかはあなたが決める。

### Pass 1 -- 機械的クリーンアップ（自動実行、10-25% 削減）

判断は一切不要。このパスで除去するのは、あってもなくてもモデルが同じように解析するフォーマットだ：

- **Markdown 除去**：`##` 見出し、`**太字**`、`- ` 箇条書き記号、`| テーブル |` パイプ記号、` ``` ` コードフェンス、`> ` 引用記号、`---` 水平線 -- すべて除去。テキストと構造は残し、装飾を取る。
- **フォーマット変換**：テーブルはセミコロン区切りの行に、箇条書きはインラインのセミコロン区切りアイテムに、リンク構文 `[テキスト](url)` は URL に縮約。
- **検証スキャン**：除去後に全文スキャンし、Markdown の残留を捕捉。16 進カラーコード（`#FF0000`）、正規表現のグロブ（`*.txt`）、シェルパイプ、YAML フロントマターはコンテンツとして認識し、そのまま保持。

テクニックカタログのテクニック 1-2 に対応。どんなドキュメントにも安全に実行可能。典型的な節約：10-25% のワード削減。

### Pass 2 -- 事実保持圧縮（承認制、追加 +5-15%）

ここからクリエイティブになる。提案された各編集は推定節約量とともに提示され、承認後にのみ適用：

- **相互参照の重複排除**（テクニック 3）：3 箇所で同じ事実が述べられている場合、1 箇所を正規の記述として残し、残りを「S6 参照」のようなポインタに置換。
- **コードブロック圧縮**（テクニック 4）：struct/class 定義をコンパクトなインライン記述に変換し、フィールド名・型・enum 値をすべて保持。例外：Python/YAML/Makefile は空白が意味を持つためそのまま保持。
- **JSON 設定圧縮**（テクニック 5）：完全な JSON サンプルをスキーマ記述に変換。
- **バージョン別詳細の削減**（テクニック 6）：サマリーテーブルとバージョン別詳細が同じ内容なら、詳細を削除。
- **モチベーションブロック圧縮**（テクニック 7）：「なぜこれが重要か」セクションを核心的な技術的洞察に凝縮。
- **設計根拠の圧縮**（テクニック 8）：4 段落の論証を主要な理由付きの 1 文に。
- **ユースケースリスト圧縮**（テクニック 9）：冗長な複数段落のユースケースを各 1 行に。
- **ASCII モックアップ圧縮**（テクニック 10）：トークンを消費する ASCII アートをコンパクトなテキスト記述に。

各編集は厳格な検証を通過する必要がある：すべての事実が残っている、AI が同一の判断を下す、曖昧さがない、各事実が正規セクションまで追跡可能。

### Pass 3 -- 人間向けコンテンツの除去（承認制、追加 +15-25%）

各候補に対する判断基準はひとつ：「これを除去したら、AI の出力品質は下がるか？」下がらなければ、それは対象だ。

- **初心者チュートリアル**（テクニック 11）：「App Store から IDE をインストールしてください。30-60 分かかります……」が、何をインストールするかのリストになる。方法は教えない。
- **練習問題と学習課題**（テクニック 12）：AI に他人の練習問題は不要。
- **日単位のスケジュール**（テクニック 13）：起床時間とコーヒーブレイクを削除。機能名と技術要件は残す。
- **コーチングとモチベーションコンテンツ**（テクニック 14）：「パニックにならないで」「お祝いしましょう！」はモデルへの情報量ゼロ。
- **検証マトリクス**（テクニック 15）：全機能が「出荷」と書かれた 30 行のテーブルが 2 行に。
- **競合比較表**（テクニック 16）：「すべての次元で勝っている」は人間を動機づけるが、モデルには無関係。
- **人間向けワークフロー例**（テクニック 17）：「あなたの 1 日はこうなる」的なウォークスルー。

Pass 3 は「何を」と「なぜ」を保持し、「人間がどうやるか」を除去する。

### Pass 4 -- 電報スタイル書き換え（明示的オプトイン、追加 +25-40%）

ドキュメント全体を超高密度の速記体で書き換える。すべての単語が情報を運ばなければならない。人間が読みやすいようにだけ存在する単語は削除。AI が唯一の消費者であるドキュメント向け。

- **ブリッジフレーズの排除**（テクニック 18）：「これは〜を意味する」「〜を可能にする」「〜のために」「注目すべきは」-- すべて削除。
- **セミコロン区切りフラグメント**（テクニック 19）：完全な文をキーバリューフラグメントに。冠詞、「です/ます」、フィラー主語を削除。
- **複数文の折りたたみ**（テクニック 20）：1 つの概念を説明する 5 つの文を本質的事実に圧縮。
- **暗黙の主語省略**（テクニック 21）：セクション見出しが「検索機能」なら、各文の冒頭に「検索機能は」と書く必要はない。
- **コンパクト記法**（テクニック 22）：オプションにスラッシュ（`immediate/batched/silent`）、フローに矢印（`offline -> local queue -> sync`）、関係にコロン（`Auth: JWT + refresh`）。
- **反復構造パターン圧縮**（テクニック 23）：パターンを 1 度記述し、以降は速記。

現代の LLM は電報スタイルの出力を完全な忠実度で解析する。セミコロン、矢印、スラッシュ区切りのオプション -- どのモデルファミリーも問題ない。

## モード

3 つのモードがあり、順に深くなる：

- **Strict Lossless**（デフォルト）：Pass 1 + Pass 2。すべての事実が残り、ドキュメントは人間にも読める。あなたと AI の両方が参照するファイルに最適 -- CLAUDE.md、共有エージェントルール、メンテナンス中の仕様書。安全で、保守的で、確実にコスト削減。

- **AI-Lossless**：Pass 3 を追加。チュートリアル、コーチング、日単位のスケジュール、検証マトリクスなど、人間の読者だけを助けるコンテンツを除去。AI が受け取るシグナルはまったく同じ。AI が消費するが自分ではめったに読み返さないシステムプロンプトやエージェント指示に最適。

- **AI-Only**：Pass 4 を追加。電報スタイルで全面書き換え。最大密度、快適な読み物ではない。毎トークンの節約が 1 日数千回のコールに掛け算される高頻度本番プロンプトに最適。

## ワークフロー

| Pass | 内容 | 承認 | 典型的な削減率 |
|------|------|------|---------------|
| 1 | Markdown フォーマット除去、テーブル/リスト変換 | 自動 | 10-25% |
| 2 | 事実の重複排除、コード/JSON/論証の圧縮 | 項目ごと | +5-15% |
| 3 | チュートリアル、コーチング、スケジュール、人間向け足場の除去 | 項目ごと | +15-25% |
| 4 | 電報スタイル全面書き換え、文の代わりにフラグメント | 明示的オプトイン | +25-40% |

典型的なプロダクト仕様書で 4 パスすべて実行：**50-65% 削減**。35,000 語のドキュメントで、毎回の API コールあたり約 25,000-32,000 トークンの節約。

## 設計方針

- **デフォルトはロスレス**：Strict Lossless がデフォルトモード。より積極的な圧縮は明示的にオプトインが必要。
- **セクション番号は神聖**：相互参照のアンカーは決して振り直されない。周囲のセクションが削除されても。「S8.7.3 参照」は常に有効。
- **承認ゲート**：機械的でないすべての編集が差分と推定節約量とともに提案される。承認なしには何も変わらない。
- **23 テクニック＋サンプル**：完全な前後比較カタログは `references/techniques.md` にある。
- **12 のエッジケースに対応**：短いドキュメント、コード密度の高いプロンプト、すでに圧縮済みの入力、非英語/CJK テキスト、埋め込み URL、多言語ドキュメント、Markdown について書かれたドキュメント、文芸/法律文書、超大規模ドキュメントなど。

## ディレクトリ構成

```
lossless-prompt-compressor/
├── SKILL.md              — 操作マニュアル（約 350 行）
└── references/
    └── techniques.md     — テクニック全カタログ（前後比較サンプル付き）
```

## 作者

[Wallny](https://github.com/wallmage)
