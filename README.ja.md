# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

AI向けの指示書、ルールファイル、企画書、プロダクト仕様書を圧縮します。事実は一切失われません。

要約ではなく圧縮です。マシンが必要とする事実はすべて残し、フォーマットの冗長性、重複、人間向けの補足をカットします。オプションでAI専用の超高密度な速記形式に書き換えることも可能です。

## モード

- **Strict Lossless**（デフォルト）：機械的クリーンアップ（Pass 1）＋承認済みの事実保持編集（Pass 2）。事実はすべて残り、人間にも読めます。
- **AI-Lossless**：Pass 3を追加。チュートリアル、コーチング、スケジュールなど人間だけが読む内容を除去。
- **AI-Only**：Pass 4を追加。電報スタイルで全面書き換え、最大密度を実現。

## ワークフロー

| Pass | タイプ | 承認 | 典型的な削減率 |
|------|--------|------|---------------|
| 1 | 機械的クリーンアップ | 自動 | 10-25% |
| 2 | 事実保持圧縮 | 項目ごと | +5-15% |
| 3 | 人間向けコンテンツ除去 | 項目ごと | +15-25% |
| 4 | 電報スタイル書き換え | 明示的オプトイン | +25-40% |

Pass 1で包装を剥がす。Pass 2で真空パック。Pass 3で人間だけが読む取説を外す。Pass 4ですべてを速記に変換。

## 設計方針

- デフォルトはStrict Lossless：要約ではなく圧縮
- セクション番号安全：相互参照のアンカーは振り直さない
- 承認ゲート式：機械的でない編集はすべてユーザーの承認が必要
- 23の番号付きテクニック、ビフォー・アフター例付き → `references/techniques.md`
- 12のエッジケース：コード多めのプロンプトから多言語ドキュメントまで対応

## ディレクトリ構成

```
lossless-prompt-compressor/
├── SKILL.md              — 操作マニュアル（約350行）
└── references/
    └── techniques.md     — テクニック全カタログ（例付き）
```

## 作者

[Wallny](https://github.com/wallmage)
