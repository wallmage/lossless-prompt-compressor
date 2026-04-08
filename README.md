# Lossless Prompt Compressor

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
