---
name: add-technique-from-reference
description: |
  Two modes: (1) failure-mode insight → new satirical technique, (2) production disaster → new entry in Real-World AI Failures. MUST trigger when the user provides an external URL and says "add this," "收录," "加个技法," "add technique," "add to README," etc. Also force-trigger for ANY link about AI coding agents, harness engineering, prompt engineering, LLM safety, agent-caused production outages, vibe coding failures, or AI agent security — even without an explicit "add" request. Core workflow: fetch → check relevance → deduplicate → draft → review with user (output draft, ask for feedback) → insert → verify.
---

<what-to-do>

1. Create a todo list (4–5 high-level steps). Do NOT create granular subtasks.

2. Fetch the URL. Use anysearch extract, web_fetch, or curl fallback. Read the full content.

3. Check relevance. If the article's topic is clearly AI coding agents / harness failures / production incidents, proceed. If it's unrelated (general tech news, non-AI topics), ask the user with `ask_user_question` whether to proceed. Do NOT silently skip or force-fit.

4. Deduplicate. Read all existing `### N.` techniques in `README.md`. For each insight: (a) same core joke as an existing technique? Stop — enrich or skip. (b) same failure mode, different example? Stop — existing technique owns this. When uncertain, ask the user with `ask_user_question`. If nothing is novel, add the reference to `references/index.md` and stop.

5. Draft both EN and ZH versions. Follow the technique structure and writing style in `.agent/references/writing-style.md`. Write English first, then translate to Chinese.

6. **Review gate — do NOT edit README files yet.** Output both drafts to the conversation. Use `ask_user_question` (header: "Review draft", multiSelect: true) with these modification directions:
   - "Section leads feel flat" — replace 首先/其次/然后 or First/Second/Then with vivid conversational leads
   - "Strengthen the epigraph" — make the opening quote sharper, more quotable
   - "Ending is formulaic" — replace the "说真的…" template with a story beat (timeline/scene/reveal)
   - "Deepen the absurdity" — push a weak paragraph further with more concrete, absurd detail
   - "'When caught' excuse needs layers" — make it a multi-layered corporate deflection cascade
   - "Chinese translation needs work" — tone, epigraph, or 被发现了就说 doesn't match EN
   - "Looks good — insert as-is"
   Apply revisions, ask again. Loop until user approves. Only then proceed to insertion.

7. After approval: add reference to `references/index.md`. Insert technique into both READMEs following `AGENTS.md` cross-reference rules (insertion point, count update, layer assignment, defense objectives). Verify `grep -c "^### [0-9]" README.md README-zh.md` returns identical counts.

</what-to-do>

<supporting-info>

## Writing reference

Technique structure, writing style rules, Chinese translation conventions, and anti-patterns are in `.agent/references/writing-style.md`. Read it when drafting.

## Insertion Rules

Follow `AGENTS.md` § Cross-Reference Rules § A (insertion point, count update, layer assignment, defense objectives). Verify with `grep -c "^### [0-9]" README.md README-zh.md`.
