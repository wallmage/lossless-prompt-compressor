# Lossless Prompt Compressor

Compression for AI instruction docs, rule files, planning docs, and product specs.

This is compression, not summarization: the skill preserves machine-relevant facts while cutting formatting overhead, redundancy, human-only scaffolding, and optionally rewriting into ultra-dense AI-only shorthand.

## Modes

- **Strict Lossless** (default): Pass 1 (mechanical cleanup) + approved Pass 2 edits (fact-preserving compression). Every fact survives. Document stays human-readable.
- **AI-Lossless**: Pass 3 on top of Strict Lossless. Removes human-only content (tutorials, coaching, schedules) while preserving everything the AI needs.
- **AI-Only**: Pass 4 on top of earlier passes. Full telegram-style rewrite for maximum density.

## Workflow

| Pass | Type | Approval | Typical reduction |
|------|------|----------|-------------------|
| 1 | Mechanical cleanup | Automatic | 10-25% |
| 2 | Fact-preserving compression | Per-item | +5-15% |
| 3 | Human-only content removal | Per-item | +15-25% |
| 4 | Telegram rewrite | Explicit opt-in | +25-40% |

Think of it as four levels: Pass 1 removes packaging. Pass 2 vacuum-seals. Pass 3 removes the instruction manual only a human would read. Pass 4 converts everything to shorthand.

## Design

- Strict lossless by default — compression, not summarization
- Section-number-safe — cross-reference anchors are never renumbered
- Approval-gated — every non-mechanical edit requires user sign-off
- 23 numbered techniques — full catalog in `references/techniques.md` with before/after examples
- 12 edge cases — from code-heavy prompts to mixed-language documents

## Structure

```
lossless-prompt-compressor/
├── SKILL.md              — Operating manual (~350 lines)
└── references/
    └── techniques.md     — Full technique catalog with examples
```

## Author

[Wallny](https://github.com/wallmage)
