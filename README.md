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

English | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## Why this exists

Every token in your system prompt is a tax on every API call. A 35,000-word instruction file means ~48,000 tokens of context eaten before the model reads a single user message. Multiply by thousands of requests per day and you're burning money on formatting, redundancy, and prose that only a human would ever need.

I built this to fix that. It takes AI instruction documents -- system prompts, CLAUDE.md files, agent rules, product specs, master plans -- and compresses them hard. Not summarizes. Compresses. Every fact, field name, constraint, and cross-reference survives intact. What gets cut is the packaging: Markdown formatting the model never needed, duplicated statements, tutorial scaffolding, motivational filler, grammatical glue that costs tokens without carrying information.

On a typical product spec you get 50-65% reduction across all four passes. Real money saved per API call, and real context window freed up for the conversation that actually matters.

This compressor also powers the compression pass inside [vibecheck](https://github.com/wallmage/vibecheck).

## What "lossless" actually means

This is not summarization. A summary decides what matters and throws away the rest. That's dangerous for instruction files -- one missing constraint and the model hallucinates a feature you explicitly ruled out.

Lossless means: if the original says "cap free-tier subsidies at 3% of monthly revenue", the compressed version says exactly the same thing. No facts dropped, reworded into ambiguity, or silently merged. Section numbers stay pinned so cross-references ("see S8.7.3") never break. The AI produces identical code and architecture decisions from either version.

Enforced by an approval gate -- every non-mechanical edit is proposed with a before/after diff and only applied after you sign off.

## The 23 techniques across 4 passes

Four progressive passes, each going deeper. You choose where to stop.

### Pass 1 -- Mechanical cleanup (automatic, 10-25% reduction)

No judgment calls here. This pass strips formatting that LLMs parse identically whether it is there or not:

- **Markdown stripping**: `##` headers, `**bold**`, `- ` bullets, `| table |` pipes, ` ``` ` code fences, `> ` blockquotes, `---` rules -- all removed. The text and structure stay; the decoration goes.
- **Format conversion**: Tables become semicolon-delimited rows. Bullet lists become inline semicolon-separated items. Link syntax `[text](url)` collapses to the URL.
- **Verification sweep**: After stripping, a full scan catches stray Markdown survivors. Hex colors (`#FF0000`), regex globs (`*.txt`), shell pipes, and YAML frontmatter are recognized as content and left alone.

Techniques 1-2 from the catalog. Safe to run on anything. Typical savings: 10-25% word reduction.

### Pass 2 -- Fact-preserving compression (approval-gated, +5-15%)

Here it gets interesting. Each proposed edit is shown with estimated savings and only applied on approval:

- **Cross-reference deduplication** (Technique 3): Facts stated in three places get stated once, with the others replaced by a pointer like "per S6".
- **Code block compression** (Technique 4): Struct/class definitions become compact inline descriptions preserving all field names, types, and enums. Exception: Python/YAML/Makefile stay verbatim because whitespace is semantic.
- **JSON config compression** (Technique 5): Full JSON examples become schema descriptions.
- **Version breakdown cuts** (Technique 6): If a summary table and a per-version breakdown say the same thing, the breakdown goes.
- **Motivational block compression** (Technique 7): "Why this matters" sections get collapsed to the core technical insight.
- **Design rationale compression** (Technique 8): Four paragraphs of reasoning become one sentence with the top reasons.
- **Use-case list compression** (Technique 9): Verbose multi-paragraph use cases become one-liners each.
- **ASCII mockup compression** (Technique 10): Token-expensive ASCII art becomes a compact text description.

Every edit must pass validation: all facts survive, the AI would make identical decisions, nothing is ambiguous, every fact traces back to its canonical section.

### Pass 3 -- Human-only content removal (approval-gated, +15-25%)

The test for each candidate: "If I remove this, will the AI produce worse output?" If no, it goes.

- **Beginner tutorials** (Technique 11): "Install your IDE from the app store. This takes 30-60 minutes..." becomes a list of what to install, not how.
- **Practice prompts and exercises** (Technique 12): The AI does not need someone else's learning exercises.
- **Day-by-day schedules** (Technique 13): Wake times and coffee breaks get cut. Feature names and technical requirements stay.
- **Coaching and motivational content** (Technique 14): "Don't panic" and "Celebrate!" carry zero information for the model.
- **Validation matrices** (Technique 15): A 30-row table where every feature says "SHIP" becomes two lines.
- **Competitive comparison tables** (Technique 16): "We win on every dimension" motivates humans, not models.
- **Step-by-step human workflow examples** (Technique 17): "What your day looks like" walkthroughs.

Pass 3 preserves WHAT and WHY. It removes HOW-for-humans.

### Pass 4 -- Telegram rewrite (explicit opt-in, +25-40%)

Full document rewrite in ultra-dense shorthand. Every word must carry information; if it only exists to make prose flow for a human reader, it goes. For documents where the AI is the sole consumer.

- **Bridge phrase elimination** (Technique 18): "This means that", "which allows", "in order to", "it's important to note that" -- all cut.
- **Semicolon-delimited fragments** (Technique 19): Full sentences become key-value fragments. Articles, "is/are", filler subjects dropped.
- **Multi-sentence collapse** (Technique 20): Five sentences explaining one concept become the essential fact.
- **Implied subject dropping** (Technique 21): When the section heading says "Search Feature", you do not need "The search feature" at the start of every sentence.
- **Compact notation** (Technique 22): Slashes for options (`immediate/batched/silent`), arrows for flow (`offline -> local queue -> sync`), colons for relationships (`Auth: JWT + refresh`).
- **Repetitive pattern compression** (Technique 23): Describe the pattern once, then use shorthand for each instance.

Modern LLMs parse telegram-style just fine. Semicolons, arrows, slash-separated options -- no model family has trouble with any of it.

## Modes

Three modes, each building on the last.

- **Strict Lossless** (default): Pass 1 + Pass 2. Every fact survives, document stays human-readable. Use for files that both you and the AI reference -- CLAUDE.md, shared agent rules, living specs.

- **AI-Lossless**: Adds Pass 3. Strips tutorials, coaching, day-by-day schedules, validation matrices -- anything only a human reader needs. Same signal to the AI. Use for system prompts and agent instructions you rarely re-read yourself.

- **AI-Only**: Adds Pass 4. Full telegram-style rewrite. Maximum density, not pleasant reading. For high-volume production prompts where every token saved multiplies across thousands of daily calls.

## Workflow

| Pass | What it does | Approval | Typical reduction |
|------|-------------|----------|-------------------|
| 1 | Strips Markdown formatting, converts tables/lists | Automatic | 10-25% |
| 2 | Deduplicates facts, compresses code/JSON/rationales | Per-item | +5-15% |
| 3 | Removes tutorials, coaching, schedules, human scaffolding | Per-item | +15-25% |
| 4 | Full telegram-style rewrite, fragments over sentences | Explicit opt-in | +25-40% |

All four passes on a typical product spec: **50-65% reduction**. On a 35,000-word document, roughly 25,000-32,000 fewer tokens per API call.

## Design principles

- **Lossless by default**: Strict Lossless is the default. You opt in to anything more aggressive.
- **Section numbers are sacred**: Cross-reference anchors never get renumbered, even when surrounding sections are removed. "See S8.7.3" keeps working.
- **Approval-gated**: Non-mechanical edits get proposed with a diff and estimated savings. Nothing changes without your sign-off.
- **23 techniques with examples**: Full before/after catalog in `references/techniques.md`.
- **12 edge cases handled**: Short documents, code-heavy prompts, already-compressed input, non-English/CJK text, embedded URLs, mixed-language documents, documents about Markdown, creative/legal prose, very large documents, and more.

## Structure

```
lossless-prompt-compressor/
├── SKILL.md              — Operating manual (~350 lines)
└── references/
    └── techniques.md     — Full technique catalog with before/after examples
```

## Author

[Wallny](https://github.com/wallmage)
