---
name: lossless-prompt-compressor
description: Lossless compression for large system prompts, master plans, product specs, and coding instructions to reduce token cost for any AI assistant — Claude, GPT, Codex, Gemini, Llama, Mistral, or any LLM. Use this skill whenever the user wants to compress a system prompt, reduce token cost of a long document fed to AI, optimize a master plan for LLM context, shrink a spec document without losing information, or fit more into a context window. Also use when the user mentions "token savings", "system prompt optimization", "compress for AI", "context window", "reduce tokens", or "lossless compression". This is a 4-pass compressor — Pass 1 is automatic, Pass 2 and 3 require human approval, Pass 4 is an optional ultra-dry telegram-style rewrite for maximum compression (requires explicit user permission).
---

# Lossless Prompt Compressor

Compress large system prompts to minimize token cost while preserving every piece of information that an AI coding assistant actually needs. Works with any LLM — Claude, GPT, Codex, Gemini, Llama, Mistral, or any model that consumes system prompts.

Four-pass process with increasing aggressiveness:

- Pass 1: Mechanical (automatic, truly lossless) — strips formatting overhead
- Pass 2: Creative (requires approval, truly lossless) — compresses verbose sections while keeping every fact
- Pass 3: High-fidelity (requires approval, lossy for humans only) — removes content that only serves human readers and has zero impact on AI work quality
- Pass 4: Ultra-dry telegram (optional, requires explicit user permission) — rewrites the entire document in telegram-style fragments for AI-only consumption, targeting maximum compression while preserving all facts and precision

Think of it as four levels: Pass 1 removes packaging. Pass 2 vacuum-seals. Pass 3 removes the instruction manual only a human would read. Pass 4 converts everything to shorthand — every word earns its place or it's gone.

## When to Use

- Long document (5K+ words) used as a system prompt or context for any AI assistant
- Reducing API costs (tokens = money)
- Fitting more content into a limited context window
- The document is a product spec, master plan, coding guide, CLAUDE.md, .cursorrules, or similar reference material

## Philosophy

The goal is NOT summarization. Summarization loses information that matters. This is compression — Pass 1 and 2 are truly lossless (every fact survives), while Pass 3 is "AI-lossless" (every fact the AI needs survives, but human-only content like coaching, tutorials, and motivational prose gets cut). Pass 4 goes further: it rewrites every surviving sentence into the most compact form a modern LLM can still parse with full fidelity — no full sentences needed, just precise fragments.

The key distinction for Pass 3: if removing a section would cause the AI to produce worse code, worse architecture decisions, or miss a spec requirement — keep it intact. If removing it would only affect a human reader's experience (motivation, tutorials, step-by-step beginner guides) — propose cutting it.

The key distinction for Pass 4: no information is deleted. Every fact, spec, number, name, and relationship survives. The compression comes entirely from rewriting prose into dry, telegram-style fragments. The document becomes unpleasant for humans to read but functionally identical for AI consumption.

## CAUTION: Preserve All Section and Subsection Numbering

NEVER strip or modify section/subsection numbers. Numbers like "1. Product Positioning", "8.7.3 Rule-Based Pre-Router", "S16.1 Tech Stack" are structural identifiers, NOT Markdown list formatting. They serve as cross-reference anchors used throughout the document (e.g., "see S8.7.3", "per S6").

Rules:
- When stripping Markdown numbered list formatting (1. , 2. ), distinguish between inline list items and section/subsection headers. Section headers have a number followed by a title on their own line.
- When deleting entire sections in Pass 2/3, do NOT renumber the remaining sections. Keep original numbers intact so cross-references remain consistent with the original source.
- Before applying Pass 1, scan the document for its numbering scheme. Catalog all section/subsection numbers. After Pass 1, verify every one survived.

## Pass 1: Mechanical Compression (automatic, no approval needed)

These transformations are safe to apply immediately because they never delete semantic content.

### 1. Strip All Markdown Formatting

System prompts don't need to be pretty — they need to be parseable. Remove all Markdown syntax that costs tokens but adds no information for the AI reader.

Remove:
- #, ##, ### header markers (replace with plain text section labels, keep any section number)
- **bold** and *italic* markers
- - bullet markers (use semicolons or natural prose instead)
- | table | pipe syntax (use semicolon-delimited inline format)
- ``` code fences (strip the fence markers but preserve all code content verbatim — code compression happens in Pass 2, Technique 4)
- > blockquote markers
- --- horizontal rules
- Numbered list formatting (1. , 2. ) ONLY for inline lists — NEVER for section/subsection headers (see CAUTION above)
- Link syntax [text](url) (keep just the URL or text, whichever is useful)

Important: Preserve blank lines between sections and paragraphs. Blank lines cost ~1 token each but provide structural signal that helps AI parse sections and maintain attention across long prompts. Not worth removing.

Convert tables to inline semicolon-delimited format:

Before (Markdown table):
| Platform | Framework | Language |
|----------|-----------|----------|
| iOS      | SwiftUI   | Swift    |

After (plain text):
Platform; Framework; Language
iOS; SwiftUI; Swift

Convert bullet lists to semicolon-separated inline text:

Before:
- Fast and purposeful
- Physics-based
- Confirm, don't decorate

After:
Fast and purposeful; Physics-based; Confirm, don't decorate

### 2. Verify Zero Markdown Remains

After stripping, search the entire document for any remaining Markdown syntax. Common survivors: stray **, leftover # at line starts, pipe | from unconverted tables, backticks around inline code.

## Pass 2: Creative Compression (requires user approval)

These transformations involve judgment. Always propose as a numbered list with estimated savings, and wait for per-item approval.

### 3. Cross-Reference Deduplication

Find facts stated multiple times. State each fact once in its canonical location, then replace all others with a short cross-reference.

Before (fact in 3 locations):
Line 196: "cap total free-tier subsidies at 3% of monthly revenue"
Line 542: "Free-tier is capped at 3% monthly revenue for cloud LLM subsidies"
Line 688: "Cap cloud LLM subsidy at 3% of monthly revenue"

After:
Line 196: "cap total free-tier subsidies at 3% of monthly revenue" (canonical)
Lines 542, 688: "Free-tier capped per S6" (compressed)

How to find duplicates: search for key numbers, percentages, time durations, and branded terms that appear more than once. Common culprits: pricing figures, performance targets, time limits, feature names repeated in overview + detail sections.

### 4. Compress Code Blocks to Inline Descriptions

Code samples in system prompts are reference material, not executable code. The AI doesn't copy-paste from the system prompt — it uses the information to guide its own code generation. Replace code blocks with compact natural-language descriptions preserving all field names, types, and relationships.

Before (20+ lines of code):
struct UserProfile {
    var name: String
    var email: String
    var role: Role  // enum: admin, editor, viewer
    var createdAt: Date
}

After (2 lines):
struct UserProfile fields: name (String), email (String), role (Role enum: admin/editor/viewer), createdAt (Date).

Preserve: all field names, types, annotations, enum values, import statements, class/protocol names.
Remove: syntactic scaffolding (braces, indentation, decorative comments).

Exception: whitespace-semantic languages (Python, YAML, Makefile) — preserve these code blocks verbatim in Pass 2. See Edge Cases for details.

### 5. Compress JSON Config Blocks

Same principle as code blocks. Replace full JSON examples with compact schema descriptions.

Before (30+ lines of JSON):
{ "version": "1.0", "regions": [ { "region": "CN", "providers": [...] } ] ... }

After (1 paragraph):
Provider config: JSON with version, updated date, regions array. Each region: region code (CN/US/EU), strategy ("direct"/"aggregator"), providers array (name, endpoint, priority, max_latency_ms, cost_per_1k_tokens), fallback_timeout_ms.

### 6. Cut Version-Specific Breakdowns That Duplicate a Summary

If the document has both a summary table AND a per-version detailed breakdown covering the same information, propose cutting the detail and keeping the summary. If any information in the detail ISN'T in the summary, compress to 1-2 lines and append.

### 7. Summarize "Why This Matters" / Motivational Blocks

Product specs often include motivational explanations. Compress each to 1-2 sentences preserving the core insight:

Before (6 lines): Why This Must Be Right From Day One: We need multi-region
support because the Great Firewall blocks all US providers. At maturity the
router manages 20-30 endpoints... (lengthy justification)

After (2 lines): Why day one: GFW blocks US providers; at maturity router
manages 20-30 endpoints for 100+ countries; 2x cost difference at scale =
profit vs loss.

### 8. Compress Design Decision Rationales

Design decisions often include 3-4 paragraphs of reasoning where 1 sentence with key reasons suffices.

### 9. Compress Verbose Use Case Lists

When the document lists 5+ detailed use cases, compress each to a one-liner. The AI needs the pattern, not full paragraphs per use case.

### 10. Compress ASCII Art / UI Mockups

ASCII mockups cost many tokens. Replace with compact text descriptions that capture layout, key elements, and any spec requirements embedded in the visual.

Before (10-line ASCII UI mockup):
+---------------------+
|  Welcome!           |
|  Your data matters. |
|  [Got it]           |
+---------------------+

After (1 line):
Welcome screen: "Your data matters" message with [Got it] button.

## Pass 3: High-Fidelity Compression (requires user approval)

Pass 3 targets content that exists to serve a human reader but has zero impact on AI coding quality. The key question for every section: "If I remove this, will the AI produce worse code or miss a spec requirement?" If no — it's a candidate.

This pass is more aggressive and can achieve an additional 15-25% reduction on top of Pass 1+2. Always propose as a numbered list.

### What to Look For

#### 11. Beginner Tutorials and Tool Installation Guides

Step-by-step instructions for installing tools, creating accounts, or learning IDEs. The AI doesn't need to know how to install software.

Before (800 words):
Day -2 — Tool Installation
Install your IDE from the app store. This takes 30-60 minutes...
Create a developer account ($99/year)...
If anything fails: Don't panic...

After (2 lines):
Day -2 — Tool Installation: Install and verify IDE, developer account, AI
coding tools, API keys, version control, analytics.

The compressed version preserves WHAT needs to be installed but removes HOW (human-only tutorial).

#### 12. Practice Prompts and Learning Exercises

Prompts designed to help a beginner learn tools ("Try this practice prompt..."). The AI doesn't need someone else's practice exercises.

#### 13. Day-by-Day Scheduling and Human Routines

Detailed schedules with wake-up times, breaks, and sleep times. The AI needs to know WHAT gets built each phase and any technical details, not WHEN the human eats dinner.

Compress day-by-day breakdowns into weekly summaries. Keep: feature names, technical requirements, key implementation details. Remove: time blocks, motivational pep talks, "commit message" suggestions, rest day descriptions.

#### 14. Coaching and Motivational Content

"Don't panic," "Celebrate!", "Don't get discouraged" — these support a human's emotional state but have no impact on AI output quality.

Compress to the actionable core only. If there is no actionable core, remove entirely.

Note: Technique 7 (Pass 2) compresses motivational blocks that contain useful facts buried in prose. Technique 14 (Pass 3) removes motivational content that has zero factual content. Decision rule: if the block contains spec facts, numbers, or technical rationale — compress in Pass 2. If it's pure emotional support — remove in Pass 3.

#### 15. Validation Tables and Checklists Already Implied by Specs

If the document has a table that validates every feature against design principles (and every feature passes), the table adds no information — features are already specified elsewhere.

Before (30-row validation table):
Feature; Criteria 1; Criteria 2; Score; Verdict
Feature A; YES; YES; 6/6; SHIP
Feature B; YES; YES; 5/6; SHIP
... (26 more rows all saying SHIP)

After (2 lines):
All 26 features passed the Design Principles Checklist.
Every feature scores YES on all Tier 1 and >=4/6 Tier 2. Verdict: SHIP all.

#### 16. Competitive Comparison Tables (Motivational)

"We win on every dimension" tables motivate the human builder but the AI builds features based on specs, not competitive positioning.

Compress to 1-2 lines capturing the competitive stance, or remove if specs already define what to build.

#### 17. Step-by-Step Workflow Examples for Humans

Minute-by-minute examples of "what your day looks like" — meant to teach a human the workflow rhythm. The AI doesn't follow a daily schedule.

Before (800 words):
11:00am: Wake up. Coffee.
12:00pm: Open document. Read the goals.
12:15pm: Open AI tool. Ask for guidance...

After (2 lines):
Daily pattern: read goals -> get AI guidance -> write code -> build -> fix
errors -> test -> commit. Build every 30min, commit every 2hr max.

### Pass 3 Decision Framework

For each candidate section, ask three questions:

1. Does this contain spec facts? (feature requirements, field names, API details, architecture decisions, data models, enum values, performance targets) -> If yes, keep the facts even if you compress surrounding prose.

2. Does this affect AI code generation quality? (coding patterns, architectural constraints, error handling requirements, naming conventions) -> If yes, keep it.

3. Is this purely for human consumption? (installation tutorials, emotional support, scheduling, practice exercises, motivational comparisons, validation that confirms what specs already say) -> If yes, propose removal or heavy compression.

When compressing (not removing), the pattern is: keep WHAT and WHY, remove HOW-for-humans. "Install IDE, AI tools, API keys" (what) is useful context. "Open the app store, click search, type the name, wait 30 minutes" (how-for-humans) is not.

## Pass 4: Ultra-Dry Telegram Compression (optional, requires explicit user permission)

Pass 4 rewrites the entire document top to bottom in telegram-style shorthand. Unlike Passes 1-3 (surgical edits), this is a full rewrite. Only for documents where the AI is the sole consumer — if the user might also reference the compressed version, stop at Pass 3.

### When to Offer Pass 4

After completing Passes 1-3, if the user wants further compression, offer Pass 4 with this explanation:

"Pass 4 rewrites everything in ultra-dry telegram style — fragments instead of sentences, no bridge phrases, maximum density. It typically cuts an additional 25-40% on top of Pass 1-3. The result reads like shorthand notes, not prose — but modern LLMs parse it with the same understanding as the original. Want me to proceed?"

Only apply Pass 4 after receiving explicit user confirmation.

### The Telegram Style

Core principle: every word must carry information. If a word exists only to make text flow nicely for a human reader, it gets cut. LLMs reconstruct meaning from context and don't need grammatical scaffolding.

#### 18. Eliminate Bridge Phrases and Connective Tissue

Remove words that connect ideas for human readability but carry zero information:

Before: "This means that the user will be able to access their data from any device,
which is important because it enables a seamless cross-platform experience."

After: "User data accessible from any device; enables cross-platform experience."

Common bridge phrases to eliminate: "this means that", "which allows", "in order to", "the reason for this is", "it's important to note that", "as mentioned earlier", "for example", "in other words", "what this enables is", "the benefit of this approach is".

#### 19. Convert Prose to Semicolon-Delimited Fragments

Full sentences become comma or semicolon-separated key-value fragments. Drop articles (a, an, the), drop "is/are/will be" where meaning is clear from context, drop "users can" padding.

Before: "The application uses a local-first architecture where all data is stored
on the device. This ensures the app works offline and provides instant response
times. Cloud sync happens in the background when connectivity is available."

After: "Local-first architecture; all data on-device; works offline, instant
response; background cloud sync when connected."

#### 20. Collapse Multi-Sentence Explanations

When a paragraph uses 3-5 sentences to explain one concept, collapse to the essential fact:

Before: "The notification system is designed to be non-intrusive. Rather than
bombarding users with alerts, it uses a gentle reminder approach. Notifications
are batched and delivered at user-preferred times. Users can customize their
preferences, choosing between immediate, batched, or silent modes."

After: "Notifications: non-intrusive, batched at user-preferred times; modes:
immediate/batched/silent, configurable in settings."

#### 21. Drop Implied Subjects and Obvious Context

When the subject is obvious from the section heading or context, don't repeat it:

Section: "8.3 Search Feature"

Before: "The search feature supports full-text search across all captured items.
The search feature also supports filtering by content type, date range, and tags."

After: "Full-text across all items; filter by type/date/tags; results list
with relevance ranking."

#### 22. Use Compact Notation Patterns

Adopt shorthand patterns that LLMs parse effortlessly:

- Lists of options: use slashes — "immediate/batched/silent" not "immediate, batched, or silent"
- Conditional logic: use arrows — "offline -> local queue -> sync on reconnect"
- Ranges: use dashes — "3-5 items" not "three to five items"
- Relationships: use colons — "Auth: JWT + refresh tokens" not "Authentication uses JWT with refresh tokens"
- Enumerations: use parenthetical lists — "contentType (schedule/reminder/expense/article/note)"

#### 23. Compress Repetitive Structural Patterns

When the document describes many similar items (features, screens, API endpoints), establish the pattern once, then use shorthand:

Before (5 paragraphs, each describing a screen):
"The Timeline screen displays a chronological list of all captured items.
At the top is a date filter bar. Each item shows a thumbnail, title, and
timestamp. Users can tap to view details or swipe to delete..."

After:
"Screen pattern: header component + scrollable list + item cards with actions.

Timeline: date filter bar; cards show thumbnail/title/timestamp; tap->detail, swipe->delete.
Search: search bar with real-time filter; cards show title/preview/relevance."

### Pass 4 Execution

Unlike Passes 2-3 (surgical edits), Pass 4 is a full document rewrite:

1. Read the entire post-Pass-3 document to understand all content and cross-references.
2. Rewrite section by section, applying all telegram techniques simultaneously.
3. Preserve all section/subsection numbers exactly.
4. Preserve all cross-references.
5. Preserve all proper nouns, technical terms, field names, enum values, URLs, and numbers exactly.
6. After rewriting, verify word count reduction and spot-check 5-10 sections against pre-Pass-4 version to confirm zero information loss.

### Pass 4 Quality Check

Verify:
- All section/subsection numbers survived
- All cross-references still point to valid sections
- No facts, specs, numbers, or names were lost
- The document is parseable — fragments are unambiguous even without full sentences
- Any modern LLM would produce identical code and architecture decisions from this version as from the original

When compressing, if a fragment could be parsed two ways, add 1-2 words to disambiguate. Precision always beats brevity.

## Edge Cases

- **Short documents (under 1,000 words):** Warn the user that compression savings will be minimal. Offer to proceed but set expectations — Pass 1 might save 50-100 words, Passes 2-3 may find nothing worth compressing.
- **Code-heavy prompts (>50% code):** Technique 4 (code compression) should be more conservative. Preserve Python/YAML/Makefile code blocks verbatim — whitespace is semantic in these languages. Only compress code that uses braces/indentation for purely syntactic structure (Swift, JSON, TypeScript).
- **Already-compressed input:** Check for compression indicators: no Markdown, telegram-style fragments, semicolon-delimited lists. If the document looks pre-compressed, report diminishing returns and offer to spot-check rather than run full passes.
- **Non-English prompts:** Word count is approximate for CJK languages. Report character count alongside word count. Compression techniques still apply — bridge phrases, redundancy, and motivational prose exist in every language.
- **Embedded URLs and file paths:** Default to keeping ALL URLs and file paths intact. In system prompts, these are almost always critical (API endpoints, config locations, documentation references). Only strip the Markdown link syntax `[text](url)`, never the URL itself.
- **Pass skipping:** Pass 1 is always required. Passes 2, 3, and 4 can each be skipped independently. If the user says "skip to Pass 4," apply Pass 1 first, then Pass 4.
- **Mixed-language documents:** Many system prompts mix English prose with non-English content (CJK product names, localization strings, multilingual examples). Preserve non-English content verbatim — compress the English scaffolding around it, not the embedded content itself.
- **Rollback:** Keep a backup before each pass. If the user wants to undo a pass, restore from the previous backup rather than trying to reverse individual edits.

## Execution Workflow

### Step 1: Baseline Measurement

Before any edits, measure the starting document. Count lines, words, and estimate tokens (rough heuristic: tokens ≈ words × 1.3 for English, × 2.5 for CJK). Save these numbers for reporting at each stage.

### Step 2: Copy to Working File

Never edit the original. Copy to a working file.

### Step 3: Apply Pass 1 (Mechanical — automatic)

Apply all Markdown stripping transformations. No user approval needed.

After Pass 1, report:
"Pass 1 complete: X words -> Y words (Z% reduction). All Markdown formatting removed. No content deleted."

CRITICAL: Verify all section/subsection numbers survived. Compare against the catalog built before Pass 1. If any were stripped, restore immediately.

### Step 4: Propose Pass 2 Optimizations

Read the entire document. For each optimization opportunity, note:
- Type (dedup, code compress, JSON compress, section cut, motivational, rationale, use cases, ASCII art)
- Exact location (section number, line range)
- What you'd change
- Estimated word savings

Present as a numbered list, grouped by type for easy review:
1. [Dedup] S6 pricing "3% of monthly revenue" repeated in 4 locations.
   Keep canonical in S6, replace others with "per S6". Saves ~40 words.
2. [Code compress] S8.7.1 struct definition (28 lines).
   Replace with 2-line inline description. Saves ~150 words.

On large documents (30+ proposals), batch into groups of 5-10 by type so the user can approve categories at a time rather than individually.

### Step 5: Get User Approval for Pass 2

Wait for per-item approval. Users may approve some and reject others.

### Step 6: Apply Approved Pass 2 Optimizations

Work through approved items. Re-measure and report.

### Step 7: Propose Pass 3 Optimizations

Read the entire document with fresh eyes, looking for human-only content per Pass 3 categories (tutorials, practice prompts, scheduling, coaching, validation tables, competitive tables, workflow examples).

For each candidate, note:
- Category (tutorial, coaching, scheduling, validation, competitive, workflow example)
- Exact location
- Proposed action: REMOVE (entirely human-only) or COMPRESS (has useful context buried in human prose)
- If COMPRESS: show before/after
- Estimated word savings

Group by category for easy review.

### Step 8: Get User Approval for Pass 3

Wait for per-item approval. Users may be protective of some sections. Respect that.

### Step 9: Apply Approved Pass 3 Optimizations

Apply all approved items. Re-measure.

### Step 10: Offer Pass 4 (Optional)

After reporting Pass 3 results, offer Pass 4 per the "When to Offer Pass 4" section above. Only proceed with explicit user confirmation.

### Step 11: Apply Pass 4 (if approved)

1. Back up the current working file.
2. Rewrite the entire document in telegram style per Pass 4 techniques.
3. Report: "Pass 4 complete: X words -> Y words (Z% additional reduction). Total from original: W%."

### Step 12: Final Measurement and Verification

Report a summary table:

Stage; Words; Est. Tokens; Reduction
Original; 37,237; ~48,400; -
After Pass 1; 33,472; ~43,500; 10%
After Pass 2; 31,126; ~40,500; 16%
After Pass 3; 25,730; ~33,400; 31%
After Pass 4; 16,200; ~21,100; 56%

Run final verification:
- Search for remaining Markdown syntax
- Spot-check 3-5 compressed sections to ensure no spec information was lost
- Confirm all cross-references still point to valid sections
- For Pass 4: verify all section/subsection numbers survived

## Rules

1. Never delete spec information. If in doubt, keep it. A few extra tokens are cheaper than a missing detail that causes the AI to hallucinate.
2. Never touch section/subsection numbering. Section numbers are cross-reference anchors, not formatting. When deleting sections in Pass 2/3, keep original numbering — never renumber.
3. Preserve all blank lines between sections. They cost almost nothing and help AI attention.
4. Don't reorder sections. The document's structure is intentional. Compress in-place.
5. Don't use lossy abbreviations like "dev" for "development" — these save 3-4 characters but reduce clarity.
6. Pass 1 is automatic. Pass 2 and 3 require approval. Always present proposals and wait for per-item approval.
7. Pass 4 requires explicit user permission. Never apply without the user clearly saying yes.
8. Work section by section. Systematic work catches more and makes fewer mistakes.
9. Measure at every stage. Report word count reduction with percentages after each pass.
10. Pass 3 is optional. If the user only wants truly lossless compression, stop after Pass 2.
11. Pass 4 is optional. Only offer after Pass 3 is complete. Always back up pre-Pass-4 version.
12. Precision over brevity in Pass 4. If a telegram fragment is ambiguous, add words to make it precise. Maximum compression at zero fidelity cost, not absolute minimum word count.

## Expected Results

Typical compression ratios on product specs and master plans:

- Pass 1 (Markdown stripping): 10-25% word reduction
- Pass 2 (creative compression): additional 5-15%
- Pass 3 (high-fidelity, human-only removal): additional 15-25%
- Pass 4 (ultra-dry telegram rewrite): additional 25-40% on top of Pass 1-3
- Total (all 4 passes): 50-65% reduction


## Compatibility

Works with any modern instruction-tuned LLM with 8K+ context (Claude, GPT, Codex, Gemini, Llama, Mistral, DeepSeek, and others). The telegram-style output from Pass 4 produces identical behavior across model families — all modern LLMs handle semicolon-delimited fragments, arrow notation, and slash-separated options without degradation.

Author: Wallny
https://github.com/wallmage
