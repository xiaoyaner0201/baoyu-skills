# Translation Workflow Details

This file provides detailed guidelines for each workflow step. Steps are shared across modes:

- **Quick**: Translate only (no steps from this file)
- **Normal**: Step 1 (Analysis) → Translate
- **Refined**: Step 1 (Analysis) → Step 2 (Draft) → Step 3 (Review) → Step 4 (Polish)
- **Normal → Upgrade**: After normal mode, user can continue with Step 3 → Step 4

All intermediate results are saved as files in the output directory.

## Step 1: Content Analysis

Before translating, deeply analyze the source material. Save analysis to `01-analysis.md` in the output directory. Focus on dimensions that directly inform translation quality.

### 1.1 Quick Summary

3-5 sentences capturing:
- What is this content about?
- What is the core argument?
- What is the most valuable point?

### 1.2 Core Content

- **Core argument**: One sentence summary
- **Key concepts**: What key concepts does the author use? How are they defined?
- **Structure**: How is the argument developed? How do sections connect?
- **Evidence**: What specific examples, data, or authoritative citations are used?

### 1.3 Background Context

- **Author**: Who is the author? What is their background and stance?
- **Writing context**: What phenomenon, trend, or debate is this responding to?
- **Purpose**: What problem is the author trying to solve? Who are they trying to influence?
- **Implicit assumptions**: What unstated premises underlie the argument?

### 1.4 Terminology Extraction

- List all technical terms, proper nouns, brand names, acronyms
- Cross-reference with loaded glossaries
- For terms not in glossary, research standard translations
- Record decisions in a working terminology table

### 1.5 Tone & Style

- Is the original formal or conversational?
- Does it use humor, metaphor, or cultural references?
- What register is appropriate for the translation given the target audience?

### 1.6 Reader Comprehension Challenges

Identify points where target readers may struggle, calibrated to the target audience:

- **Domain jargon**: Technical terms that lack widely-known translations or are meaningless when translated literally
- **Cultural references**: Idioms, historical events, pop culture, social norms specific to the source culture
- **Implicit knowledge**: Background context the original author assumes but target readers may lack
- **Wordplay & metaphors**: Figurative language that doesn't carry over across languages
- **Named concepts**: Theories, effects, or phenomena with coined names (e.g., "comb-over effect", "Dunning-Kruger effect")
- **Cognitive gaps**: Counterintuitive claims or expectations vs. reality that need framing for target readers

For each identified challenge, note:
1. The original term/passage
2. Why it may confuse target readers
3. A concise plain-language explanation to use as a translator's note

### 1.7 Translation Challenges

- Structural challenges (wordplay, ambiguity, puns that don't translate)
- Passages where literal translation would lose meaning
- Content where the author's voice or humor requires creative adaptation

**Save `01-analysis.md`** with:
```
## Quick Summary
[3-5 sentences]

## Core Content
Core argument: [one sentence]
Key concepts: [list]
Structure: [outline]

## Background Context
Author: [who, background, stance]
Writing context: [what this responds to]
Purpose: [goal and target audience]
Implicit assumptions: [unstated premises]

## Terminology
[term → translation, ...]

## Tone & Style
[assessment]

## Comprehension Challenges
- [term/passage] → [why confusing] → [proposed note]
- ...

## Translation Challenges
[structural issues, creative adaptation needs]
```

## Step 2: Assemble Translation Prompt

Main agent reads `01-analysis.md` and assembles a complete translation prompt using [references/subagent-prompt-template.md](subagent-prompt-template.md). Inline content background, merged glossary, and comprehension challenges into the prompt. Save to `02-prompt.md`.

This prompt is used by the subagent (chunked) or by the main agent itself (non-chunked).

## Step 3: Initial Draft

Save to `03-draft.md` in the output directory.

For chunked content, the subagent produces this draft (merged from chunk translations). For non-chunked content, the main agent produces it directly.

Translate the full content following `02-prompt.md`:

- Use the terminology decisions from Step 1 consistently
- Match the identified tone and register
- Break complex sentences into natural target-language patterns
- Preserve all formatting and structure
- First occurrence of technical terms: translated term (original in parentheses)
- Add translator's notes for comprehension challenges identified in Step 1: use parentheses with a plain-language explanation, e.g., `译文（English original，通俗解释）`
- Only annotate where genuinely needed — do not over-explain obvious terms

## Step 4: Review

The main agent reviews the draft. Save reviewed version to `04-review.md`.

Systematically review against these criteria:

### Accuracy Check
- Compare each paragraph against the original
- Verify all facts, numbers, dates, and proper nouns
- Ensure no content was accidentally added, removed, or altered
- Check that technical terms match glossary consistently throughout

### Naturalness Check
- Read the translation as if it were original content (not a translation)
- Flag sentences that sound like "translationese" — awkward word order, calques, unnatural phrasing
- Check paragraph transitions and logical flow
- Verify sentence length feels natural for target language

### Terminology Consistency
- Verify each glossary term is translated the same way throughout
- Check proper nouns are handled consistently (transliterated vs. kept in original)
- Verify annotations appear on first occurrence only

### Cultural Adaptation & Translator's Notes
- Are cultural references explained where needed?
- Do metaphors and idioms work in the target language?
- Are any references potentially confusing or offensive in the target culture?
- Check translator's notes: are they accurate, concise, and genuinely helpful?
- Identify any missed comprehension challenges that should have notes
- Remove over-annotations on terms that are obvious to the target audience

## Step 5: Polish

Save final version to `translation.md`.

Final pass addressing all issues from the review:

- Fix all accuracy issues found in review
- Rewrite unnatural sentences for fluency
- Improve transitions between paragraphs
- Ensure the translation reads as engaging, native-quality content
- Verify formatting is preserved correctly
- For storytelling-style content: ensure the narrative flow draws readers in
- Final consistency check on terminology

## Subagent Responsibility

Each subagent (one per chunk) is responsible **only** for producing the initial draft of its chunk (Step 3). The main agent assembles the shared prompt (Step 2), spawns all subagents in parallel, then takes over for review (Step 4) and polish (Step 5). The main agent may delegate review or polish to subagents at its own discretion.

## Chunked Refined Translation

When content exceeds the chunk threshold (see Defaults in SKILL.md) and uses refined mode:

1. Main agent runs analysis (Step 1) on the **entire** document first → `01-analysis.md`
2. Main agent assembles translation prompt → `02-prompt.md`
3. Split into chunks → `chunks/`
4. Spawn one subagent per chunk in parallel (each reads `02-prompt.md` for shared context) → merge all results into `03-draft.md`
5. Main agent reviews the merged draft → `04-review.md`
6. Main agent polishes → `translation.md`
7. Final cross-chunk consistency check:
   - Check terminology consistency across chunk boundaries
   - Verify narrative flow between chunks
   - Fix any transition issues at chunk boundaries
