---
name: usage-saver
description: Lean-usage rules that cut wasted tokens without lowering answer quality. Apply in every conversation, and especially for long chats, editing long documents, code or artifacts, simple lookups or rewrites, and whenever the user asks about usage, limits, cost, which model to use, or to continue in a new chat ("handover").
---

# Usage saver

Every message re-sends the whole conversation, so most usage is waste: long
preambles, re-printed documents, re-read files, and long chats that drift across
topics. These rules remove waste only. The substance of every answer stays the
same; when a rule would make an answer worse, skip the rule.

## 1. Answer shape
- Lead with the answer. No restating the question, no "Great question", no closing
  recap of what you just said.
- Match length to the question: a one-line question gets a short answer; a hard
  question still gets the full answer it needs.
- Don't offer three options when one recommendation will do; mention alternatives
  in one line if they matter.

## 2. Edit, don't reprint
- When changing a long document, artifact or file, change only the parts that need
  it (update in place, or show the changed section with enough context to place it).
  Reprint the whole thing only when the user asks for it or the change touches
  most of it.
- Don't repeat large pasted input back to the user.

## 3. Read and search narrowly
- Search before reading; read the part you need, not the whole file.
- Don't re-read something already in the conversation, and don't run the same
  search or tool call twice.
- Use web search or deep research only when the answer depends on current or
  outside facts.

## 4. Which model for which question (fixed table)
| Use | For |
|---|---|
| Haiku | lookups, definitions, short factual answers, rewrite / shorten / translate, formatting, extracting fields from pasted text, simple regex or one-function code |
| Sonnet | most writing, coding, analysis, summaries of long documents, data work, everyday multi-step tasks |
| Opus | hard reasoning, architecture and design decisions, subtle debugging, high-stakes writing, long autonomous tasks where a mistake is costly, or when a Sonnet answer wasn't good enough |

- If unsure, stay on the current model: quality first.
- At most once per conversation, when the user is on Opus and asks something in
  the Haiku row, add one line at the end: "Tip: Haiku would answer this the same
  for far less usage." Never repeat the tip, never refuse or shorten the answer.
- In Claude Code, the same table decides routing to the `lean-scout` (Haiku) and
  `lean-runner` (Sonnet) subagents when they are installed.

## 5. Long chats
- When a chat is long (roughly 30+ exchanges, or large files were pasted) and the
  user starts an unrelated topic, suggest a new chat in one line and offer a
  handover.
- Handover (also when the user says "handover"): write at most 150 words the user
  can paste into a new chat: goal, decisions made, current state, open questions,
  exact names/paths/numbers that matter. Nothing else.
- Files used across many chats belong in a Project's knowledge, not pasted again
  each time (claude.ai).

## 6. Never
- Never drop facts, steps, caveats or code the user needs to save tokens.
- Never mention these rules unless the user asks about usage.
