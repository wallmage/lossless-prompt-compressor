---
name: lossless-prompt-compressor
description: "Compress AI instruction documents, rule files, planning docs, product specs, and operational manuals while preserving machine-relevant facts. Use when the user wants lower token cost, better context-window fit, prompt optimization, \"lossless compression\", \"reduce tokens\", \"compress for AI\", or similar. Default to Strict Lossless mode: Pass 1 automatic mechanical cleanup plus approval-gated Pass 2 fact-preserving compression. Only enter AI-Lossless Pass 3 or AI-Only Pass 4 when the user explicitly wants more aggressive compression."
---

# Lossless Prompt Compressor

Compress AI-facing documents without losing information the model needs to follow instructions accurately.

Four-pass process with increasing aggressiveness. Think of it as four levels: Pass 1 removes packaging. Pass 2 vacuum-seals. Pass 3 removes the instruction manual only a human would read. Pass 4 converts everything to shorthand — nothing survives unless it carries meaning.

## Modes

- Strict Lossless: Pass 1 plus approved Pass 2 edits. Preserve all facts and keep the document human-usable. This is the default.
- AI-Lossless: Pass 3 on top of Strict Lossless. Remove or compress human-only scaffolding while preserving what the model needs.
- AI-Only: Pass 4 on top of earlier passes. Rewrite for maximum density, not pleasant human reading.

Do not move beyond Strict Lossless unless the user clearly wants more compression.

## When to Use

Use freely for: system prompts, AI coding guides, agent instruction files, product specs written for AI consumption, master plans, operating manuals, onboarding docs, or reference docs fed to an LLM.

Warn before proceeding if:
- The input is under 1,000 words and likely has limited savings.
- The input already looks compressed.
- The input is fiction, legal prose, or policy language where wording itself is the artifact.
- The input is mostly executable code and the user expects code reuse rather than descriptive compression.

For non-AI prose, offer Pass 1 only unless the user clearly confirms they still want deeper compression.

## Hard Preservation Rules

1. Never renumber sections or subsections, even if sections are removed. Section numbers are cross-reference anchors ("see S8.7.3", "per S6"), not formatting.
2. Never delete a fact just because it feels repetitive unless the same fact survives canonically elsewhere and the replacement clearly points back to it.
3. Never strip URLs, file paths, identifiers, API names, class names, field names, enum values, version numbers, limits, percentages, or dates unless the user explicitly approves.
4. Never treat literal Markdown examples as disposable formatting when the document teaches Markdown or contains syntax examples.
5. Never compress whitespace-semantic code blocks (Python, YAML, Makefile) into prose unless the user explicitly asks for that tradeoff.
6. Prefer keeping a few extra tokens over introducing ambiguity.

## Pass 1: Mechanical Compression (automatic)

These transformations never delete semantic content — safe to apply immediately.

### What to Remove

- `#`, `##`, `###` header markers (keep heading text and any section number)
- `**bold**` and `*italic*` markers
- `- ` bullet markers (use semicolons or natural prose instead)
- `| table |` pipe syntax (convert to semicolon-delimited inline format)
- ` ``` ` code fences (strip the fence markers, preserve all code content verbatim — code compression happens in Pass 2)
- `> ` blockquote markers
- `---` horizontal rules (NOT YAML frontmatter delimiters — if the document begins with `---`, preserve the frontmatter block intact)
- Numbered list formatting (`1. `, `2. `) ONLY for inline lists — NEVER for section/subsection headers
- Link syntax `[text](url)` (keep the URL or text, whichever is useful)

Keep blank lines between sections and paragraphs — they cost ~1 token each but provide structural signal that helps AI parse long prompts.

### Key Examples

Table conversion:

Before:
| Platform | Framework | Language |
|----------|-----------|----------|
| iOS      | SwiftUI   | Swift    |

After:
Platform; Framework; Language
iOS; SwiftUI; Swift

Bullet list conversion:

Before:
- Fast and purposeful
- Physics-based
- Confirm, don't decorate

After:
Fast and purposeful; Physics-based; Confirm, don't decorate

### Watch Closely

- Distinguish section numbering from list numbering. Section headers have a number followed by a title on their own line. Before Pass 1, catalog all section/subsection numbers. After Pass 1, verify every one survived.
- Hash in hex colors (#FF0000), asterisks in regex or globs (*.txt), pipes in shell commands or truth tables — these are content, not Markdown. When uncertain, preserve.
- Preserve YAML frontmatter if it exists at the top of the file.
- Content inside preserved code blocks: Python comments (#), shell pipes (|), and inline code references are legitimate content, not Markdown survivors.

### Pass 1 Reporting

After Pass 1, report:
"Pass 1 complete: X words -> Y words (Z% reduction). All Markdown formatting removed. No content deleted."

## Pass 2: Fact-Preserving Compression (requires approval)

These transformations involve judgment. Propose as a numbered list with estimated savings and wait for per-item approval.

### Technique Categories

- Deduplicate repeated facts: keep one canonical statement, replace others with cross-references
- Compress code blocks to inline descriptions: preserve field names, types, and relationships
- Compress JSON config blocks to compact schema descriptions
- Cut version-specific breakdowns that duplicate a summary
- Collapse long rationales to one sentence preserving the real reasons
- Compress verbose use-case lists to one-liners per case
- Replace ASCII mockups with compact layout descriptions
- Compress motivational blocks that also contain real technical facts

See [references/techniques.md](references/techniques.md) for the full technique catalog with before/after examples for each category.

### Key Examples

Cross-reference deduplication:

Before (fact in 3 locations):
Line 196: "cap total free-tier subsidies at 3% of monthly revenue"
Line 542: "Free-tier is capped at 3% monthly revenue for cloud LLM subsidies"
Line 688: "Cap cloud LLM subsidy at 3% of monthly revenue"

After:
Line 196: "cap total free-tier subsidies at 3% of monthly revenue" (canonical)
Lines 542, 688: "Free-tier capped per S6" (compressed)

Code block compression:

Before (20+ lines):
struct UserProfile {
    var name: String
    var email: String
    var role: Role  // enum: admin, editor, viewer
    var createdAt: Date
}

After (2 lines):
struct UserProfile fields: name (String), email (String), role (Role enum: admin/editor/viewer), createdAt (Date).

Exception: whitespace-semantic languages (Python, YAML, Makefile) — preserve verbatim.

### Validation Criteria

Approve a Pass 2 item only if all of these remain true:
- Every fact still exists.
- The AI could make the same coding and architecture decisions after the edit.
- The rewrite is not ambiguous.
- A human could still trace the fact back to its canonical section.

If any are uncertain, keep the original.

### Proposal Format

```text
1. [Dedup] S6, lines 542 and 688
   Original: two repeated statements of the same subsidy cap.
   Replacement: "Free-tier subsidy cap: per S6."
   Reason: canonical fact already exists in S6; removes duplication only.
   Est. savings: ~40 words. Risk: low.
```

On large documents (30+ proposals), batch into groups of 5-10 by type for easier review.

## Pass 3: High-Fidelity Compression (optional, requires approval)

Pass 3 removes or sharply compresses material that helps human readers but does not improve model output. The key question: "If I remove this, will the AI produce worse code or miss a spec requirement?" If no — it's a candidate.

### Typical Targets

- Beginner tutorials and tool installation guides
- Practice prompts and learning exercises
- Day-by-day schedules, wake/sleep routines
- Coaching and motivational content
- Validation matrices that only confirm what specs already say
- Competitive comparison tables that add positioning but no implementation guidance
- Step-by-step workflow examples for humans

### Key Example

Tutorial compression:

Before (800 words):
Day -2 — Tool Installation
Install your IDE from the app store. This takes 30-60 minutes...
Create a developer account ($99/year)...
If anything fails: Don't panic...

After (2 lines):
Day -2 — Tool Installation: Install and verify IDE, developer account, AI
coding tools, API keys, version control, analytics.

The compressed version preserves WHAT needs to be installed but removes HOW (human-only tutorial).

### Decision Test

For each candidate, ask:
1. Does it contain a requirement, constraint, field, number, or architecture decision? Keep the useful content.
2. Would removing it make the AI produce worse code, plans, or miss a spec? Keep it.
3. Is the remaining value mostly emotional support, tutorial scaffolding, or human scheduling? Propose removal or heavy compression.

When compressing (not removing), keep WHAT and WHY. Remove HOW-for-humans.

### Pass 2 vs Pass 3 Boundary

Pass 2 compresses motivational blocks whose primary purpose is factual — spec facts, numbers, or technical rationale buried in motivational prose. Pass 3 removes blocks whose primary purpose is emotional support. For mixed blocks (90% motivation with one incidental fact): extract the fact into the nearest relevant section, then remove the block in Pass 3.

### Proposal Format

```text
1. [Tutorial] S2.4 Tool setup
   Action: COMPRESS
   Original: installation walkthrough plus encouragement text.
   Replacement: keep required tools and accounts; remove step-by-step guidance.
   Reason: preserves implementation prerequisites while removing tutorial scaffolding.
   Est. savings: ~220 words.
```

Use `REMOVE` only when the section is genuinely human-only.

## Pass 4: Telegram Rewrite (optional, requires explicit permission)

Pass 4 rewrites the entire document top to bottom in telegram-style shorthand. Unlike Passes 1-3 (surgical, section-by-section edits), this is a full rewrite. Only for documents where the AI is the sole consumer — if the user might also reference the compressed version, stop at Pass 3.

### When to Offer

After completing earlier passes, if the user wants further compression:

"Pass 4 rewrites everything in ultra-dry telegram style — fragments instead of sentences, no bridge phrases, maximum density. It typically cuts an additional 25-40% on top of Pass 1-3. The result reads like shorthand notes, not prose — but modern LLMs parse it with full fidelity. Want me to proceed?"

### Style Rules

Core principle: every word must carry information. If a word exists only to make text flow nicely for a human reader, it gets cut. LLMs reconstruct meaning from context without grammatical scaffolding.

- Drop bridge phrases and connective tissue ("this means that", "which allows", "in order to", "it's important to note that")
- Prefer semicolon-delimited fragments over full sentences
- Drop articles (a, an, the) and "is/are/will be" where meaning is clear from context
- Drop obvious subjects when the section heading provides context
- Use compact notation: slashes for options (immediate/batched/silent), arrows for flow (offline -> local queue -> sync), dashes for ranges (3-5), colons for relationships (Auth: JWT + refresh)
- Keep proper nouns, numbers, URLs, file paths, field names, and section numbers exact
- Add a few words back if a fragment could be interpreted two ways — precision beats brevity

### Key Examples

Bridge phrase removal:

Before: "This means that the user will be able to access their data from any device,
which is important because it enables a seamless cross-platform experience."

After: "User data accessible from any device; enables cross-platform experience."

Prose to fragments:

Before: "The application uses a local-first architecture where all data is stored
on the device. This ensures the app works offline and provides instant response
times. Cloud sync happens in the background when connectivity is available."

After: "Local-first architecture; all data on-device; works offline, instant
response; background cloud sync when connected."

See [references/techniques.md](references/techniques.md) for the complete set of telegram techniques with additional examples.

### Execution

1. Read the entire post-Pass-3 document to understand all content and cross-references.
2. Rewrite section by section, applying all telegram techniques simultaneously.
3. Preserve all section/subsection numbers, cross-references, proper nouns, field names, enum values, URLs, and numbers exactly.
4. After rewriting, verify word count reduction and spot-check 5-10 sections against pre-Pass-4 version to confirm zero information loss.

### Quality Check

Verify:
- All section/subsection numbers survived
- All cross-references still point to valid sections
- No facts, specs, numbers, or names were lost
- Fragments are unambiguous even without full sentences
- Any modern LLM would produce identical code and architecture decisions from this version as from the original

## Edge Cases

- **Short documents (under 1,000 words):** Warn that savings will be minimal. Pass 1 might save 50-100 words; Passes 2-3 may find nothing worth compressing.
- **Code-heavy prompts (>50% code):** Code compression should be more conservative. Preserve Python/YAML/Makefile code blocks verbatim — whitespace is semantic. Only compress code with braces/indentation for purely syntactic structure (Swift, JSON, TypeScript).
- **Already-compressed input:** Check for compression indicators: no Markdown, telegram-style fragments, semicolon-delimited lists. Report diminishing returns and offer to spot-check rather than run full passes.
- **Non-English prompts:** Word count is approximate for CJK languages. Report character count alongside word count. Compression techniques still apply — bridge phrases, redundancy, and motivational prose exist in every language. Token estimation: words × 1.3 for English, × 2.5 for CJK.
- **Embedded URLs and file paths:** Keep ALL URLs and file paths intact. In system prompts, these are almost always critical. Only strip the Markdown link syntax `[text](url)`, never the URL itself.
- **Pass skipping:** Pass 1 is always required. Passes 2, 3, and 4 can each be skipped independently. If the user says "skip to Pass N," apply Pass 1 first (required), then jump to the requested pass.
- **Mixed-language documents:** Preserve non-English content verbatim — compress the English scaffolding around it, not the embedded content itself.
- **Documents about Markdown:** Treat literal Markdown syntax inside examples and instructional passages as semantic content. Only strip Markdown used for the document's own formatting.
- **Non-Markdown uses of Markdown characters:** Hash in hex colors (#FF0000), asterisks in regex or globs (*.txt), pipes in truth tables or shell commands — scan for these before stripping. When uncertain, preserve.
- **Creative writing or legal prose:** Warn the user and offer Pass 1 only if they confirm.
- **Very large documents (exceeding context window):** Process in chunks by top-level section. Apply Pass 1 to each chunk independently, then reassemble before Passes 2-4 (which need full-document context for deduplication).
- **Rollback:** Keep a backup before each pass. Restore from backup rather than trying to reverse individual edits.

## Operating Sequence

1. Measure baseline: lines, words, estimated tokens (words × 1.3 for English, × 2.5 for CJK). For mixed-language text, report character count too.
2. Copy to working file. Never edit the original in place.
3. Apply Pass 1. Report results.
4. Propose Pass 2 candidates grouped by type, with estimated savings per item.
5. Apply only user-approved items. Re-measure and report the Strict Lossless result.
6. If the user wants more: propose Pass 3 candidates. Apply only approved items. Report.
7. If the user wants maximum compression: offer Pass 4 per the "When to Offer" section. Back up before rewriting. Report.
8. Deliver final summary table with all stages.

If the user only wants analysis or an audit, inspect and propose changes without rewriting.

## Final Reporting

Always finish with a summary table:

Stage; Words; Est. Tokens; Reduction
Original; 37,237; ~48,400; -
After Pass 1; 33,472; ~43,500; 10%
After Pass 2; 31,126; ~40,500; 16%
After Pass 3; 25,730; ~33,400; 31%
After Pass 4; 16,200; ~21,100; 56%

Also report:
- Mode delivered: Strict Lossless, AI-Lossless, or AI-Only
- What was approved vs skipped
- Whether any sections were preserved due to ambiguity risk
- Whether the final output is still human-friendly or now AI-only

## Rules

1. Never delete spec information. A few extra tokens are cheaper than a missing detail that causes the AI to hallucinate.
2. Never touch section/subsection numbering. When deleting sections in Pass 2/3, keep original numbers — never renumber.
3. Preserve blank lines between sections. They cost almost nothing and help AI attention.
4. Don't reorder sections. The document's structure is intentional. Compress in-place.
5. In Passes 1-3, don't use lossy abbreviations like "dev" for "development" — saves 3-4 characters but reduces clarity. Pass 4 permits common technical abbreviations (dev, config, auth, impl, etc.).
6. Pass 1 is automatic. Pass 2 and 3 require approval. Always present proposals and wait.
7. Pass 4 requires explicit user permission. Always back up the pre-Pass-4 version.
8. Work section by section. Systematic work catches more and makes fewer mistakes.
9. Measure at every stage. Report word count and estimated token reduction with percentages.
10. Pass 3 is optional. If the user only wants truly lossless compression, stop after Pass 2.
11. Precision over brevity in Pass 4. If a telegram fragment is ambiguous, add words. Maximum compression at zero fidelity cost.

## Expected Savings

Typical compression ratios on product specs and master plans:

- Pass 1 (Markdown stripping): 10-25% word reduction
- Pass 2 (creative compression): additional 5-15%
- Pass 3 (high-fidelity, human-only removal): additional 15-25%
- Pass 4 (ultra-dry telegram rewrite): additional 25-40% on top of Pass 1-3
- Total (all 4 passes): 50-65% reduction

On a 35,000-word document, Passes 1-3 save 10,000-17,000 words. Adding Pass 4 can bring the total down to 12,000-17,000 words — roughly 25,000-32,000 fewer tokens per API call.

Do not promise these numbers on already-lean inputs.

## Compatibility

Works with any modern instruction-tuned LLM with 8K+ context. The telegram-style output from Pass 4 produces identical behavior across model families — all modern LLMs handle semicolon-delimited fragments, arrow notation, and slash-separated options without degradation.

## Reference Files

- [references/techniques.md](references/techniques.md): complete technique catalog with before/after examples for all 23 numbered techniques across all four passes

Author: Wallny
https://github.com/wallmage
