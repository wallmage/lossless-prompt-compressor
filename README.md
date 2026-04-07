# Lossless Prompt Compressor

4-pass compression for system prompts. 50-65% token reduction without losing a single fact. Works with any LLM.

## What it does

Compresses large system prompts (CLAUDE.md, .cursorrules, master plans, product specs) to minimize token cost while preserving every piece of information the AI actually needs.

| Pass | Type | Approval | Typical reduction |
|------|------|----------|-------------------|
| 1 | Mechanical — strip Markdown formatting | Automatic | 10-25% |
| 2 | Creative — deduplicate, compress code blocks | Per-item | +5-15% |
| 3 | High-fidelity — remove human-only content | Per-item | +15-25% |
| 4 | Ultra-dry telegram — rewrite for AI-only consumption | Explicit opt-in | +25-40% |

**Total: 50-65% reduction** on a typical 35,000-word document.

## Philosophy

This is **compression**, not summarization. Passes 1-2 are truly lossless — every fact survives. Pass 3 is "AI-lossless" — human tutorials and motivation get cut, but every spec detail the AI needs stays intact. Pass 4 rewrites everything in telegram-style shorthand that any modern LLM parses identically.

## Compatibility

Works with any modern LLM:
- Claude (Opus, Sonnet, Haiku)
- GPT (GPT-4, GPT-4o, o1, o3)
- Codex
- Gemini (Pro, Ultra, Flash)
- Llama 3+
- Mistral / Mixtral
- DeepSeek
- Any instruction-tuned model with 8K+ context

## How to use

1. Copy [`SKILL.md`](SKILL.md)
2. Add it as a skill in Claude Code / Cowork, or paste as a system prompt in any LLM
3. Give it a document and say "compress this"

## Author

[Wallny](https://github.com/wallmage)
