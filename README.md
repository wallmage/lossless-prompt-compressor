# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111827?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

Compress AI instruction docs, rule files, planning docs, and product specs. No facts lost.

This is compression, not summarization. Machine-relevant facts stay; formatting overhead, redundancy, and human-only scaffolding get cut. Optionally rewrites into ultra-dense AI-only shorthand.

## Modes

- **Strict Lossless** (default): Mechanical cleanup (Pass 1) plus approved fact-preserving edits (Pass 2). Every fact survives. Still human-readable.
- **AI-Lossless**: Adds Pass 3. Strips tutorials, coaching, schedules — anything only a human would read.
- **AI-Only**: Adds Pass 4. Full telegram-style rewrite for maximum density.

## Workflow

| Pass | Type | Approval | Typical reduction |
|------|------|----------|-------------------|
| 1 | Mechanical cleanup | Automatic | 10-25% |
| 2 | Fact-preserving compression | Per-item | +5-15% |
| 3 | Human-only content removal | Per-item | +15-25% |
| 4 | Telegram rewrite | Explicit opt-in | +25-40% |

Pass 1 removes packaging. Pass 2 vacuum-seals. Pass 3 drops the instruction manual only a human needs. Pass 4 converts everything to shorthand.

## Design

- Strict lossless by default: compression, not summarization
- Section-number-safe: cross-reference anchors never get renumbered
- Approval-gated: every non-mechanical edit needs user sign-off
- 23 numbered techniques with before/after examples in `references/techniques.md`
- 12 edge cases covering code-heavy prompts to mixed-language documents

## Structure

```
lossless-prompt-compressor/
├── SKILL.md              — Operating manual (~350 lines)
└── references/
    └── techniques.md     — Full technique catalog with examples
```

## Author

[Wallny](https://github.com/wallmage)
