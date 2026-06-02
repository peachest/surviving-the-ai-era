---
name: add-technique-from-reference
description: |
  Two modes: (1) failure-mode insight → new satirical technique, (2) production disaster → new entry in Real-World AI Failures. MUST trigger whenever the user provides ANY external URL (article, blog, video, paper, incident report, news story) and says anything like "add this," "新技法," "add technique," "add incident," "add to README," "加个技法," "收录," "添加引用," "add reference," "收录这个," "收一下这个," "放到项目里," "加到项目中," or clearly wants to expand the project from an external source. Also force-trigger for ANY link about AI coding agents, harness engineering, prompt engineering, LLM safety, agent-caused production outages, vibe coding failures, Cursor/Claude Code/Copilot incidents, or AI agent security research — even if the user does not explicitly ask to "add" it. Core workflow: fetch URL → check relevance (ask_user_question if content does not match project themes) → plan with todo → deduplicate (ask_user_question if uncertain) → classify → write → insert → verify.
---

# Add Technique From Reference

## Step 0: Plan with `todo`

Before starting, create a lightweight todo task list (3-5 items) to track progress. Mark each `in_progress` before starting, `completed` right after finishing. Exactly one task in_progress at a time.

Typical plan:
```text
1. Fetch and extract insights from the URL
2. Add reference URL to references/index.md
3. Deduplicate against existing techniques; classify as technique vs incident
4. Write content (EN + ZH) and insert into both READMEs
5. Update cross-references and verify
```

Do NOT create overly granular tasks — keep it to this high-level flow.

## Workflow

### Step 1: Fetch and Read the Reference

Fetch the URL content. Try tools in this order:
1. **WebSearch / web_fetch** — use first if available.
2. **anySearch skill** or similar third-party fetch.
3. **`curl` fallback** — `curl -sL <url> | lynx -dump -stdin` for HTML; `pdftotext` for PDFs.

### Step 1b: Check Relevance — Ask User If Unsure

After reading the content, ask: does the article's core insight belong in this project (AI coding agent failures, satirical defensive programming)?

If the article is about something else entirely (e.g., general tech news, non-AI security, frontend design, Python vs Go debate, Kubernetes migration, etc.) — **do NOT silently skip or force-fit it.** Use `ask_user_question`:

```
question: "This article is about [X]. It doesn't seem to match the project's focus on AI coding agent failures. Do you still want to add it?"
header: "Intent check"
options:
  - "Yes, find a connection and add as technique/incident"
  - "No, skip it"
  - "Let me explain what I want"
```

If the article's content clearly matches the project themes (AI coding agents, harness failures, production incidents), proceed directly. Only ask when it's uncertain.

### Step 2: Deduplicate Before Writing

Read all existing `### N.` techniques in `README.md`. For each insight you extracted, check:

| Question | If Yes | If No |
|---|---|---|
| Is this the **core joke** of an existing technique? | **Stop.** Enrich existing technique or skip. | Continue. |
| Is this a different **example** of the same failure mode? | **Stop.** Existing technique owns this territory. | Proceed. |

**When uncertain, use `ask_user_question`:**
```
question: "This insight overlaps with technique [N]. Novel enough for a new technique?"
header: "Dedup check"
options:
  - "Yes — distinct enough"
  - "No — enrich existing instead"
  - "Not sure"
```

Only proceed if the insight is genuinely novel. If none are novel, add the reference to `references/index.md` and stop.

### Step 3: Draft the Technique in English First

Write the technique following the exact structure and style below. Write English first, then translate to Chinese.

### Step 4: Insert and Update Cross-References

Follow the cross-reference rules in `AGENTS.md` § Cross-Reference Rules → **A. When Adding a Technique**. That section covers insertion point, count update, layer assignment, defense objectives, and verification — do not duplicate those rules here.

---

## Technique Structure

Every technique follows this exact template:

```markdown
### N. Title (English)

> *"Epigraph — a satirical one-liner."*
> — Attribution (can be fictional, absurd, or real)

[Opening paragraph: set up what "smart people" do, then declare "We are not
smart people" / "We do the opposite." This establishes the inverted world.]

[Body: 2–4 sub-sections, each starting with a bold phrase followed by natural
prose. No 1. 2. 3. numbering — use narrative flow. Sub-sections might be:
**First, ...** / **Second, ...** / **Then, ...** / **Never ...** /
**Advanced technique: ...** / **Even better: ...** / **Bonus: ...** ]

[Closing paragraph: often self-deprecating — "Honestly, you probably don't
need to ..." or a twist that reveals the technique is already happening
everywhere.]

> When caught, say: "The excuse — delivered with a straight face."
```

## Writing Style — Deadpan Satire

This is the core. Every line must follow these principles. The style is modeled on Roedy Green's *How to Write Unmaintainable Code* — absurd advice delivered with complete seriousness, never winking at the reader.

### Core Rules

1. **Second-person, conversational.** Use "you" / "we" / "your boss" — never third-person academic ("the agent fails to distinguish"). Talk directly to the reader like a jaded senior dev corrupting a junior over drinks.

2. **Never explain the joke.** No emoji, no "this is satire" disclaimers in the technique body. No meta-commentary like "Here's the thing," "说白了," or "你没破坏任何东西" — if a sentence tells the reader what the mechanism is, delete it. The absurdity must stand on its own. The reader should only realize it's satire halfway through.

3. **No numbered lists.** Sub-sections flow as narrative paragraphs. Use bold lead-ins (**First, length.** / **Second, content.** / **Never update.**) — never `1.` `2.` `3.`.

4. **Concrete to the point of absurdity.** Don't say "add outdated information." Say "The project migrated from Express 4 to Fastify, from MongoDB to Postgres, from a monolith frontend into seven micro-frontends — but your AGENTS.md should faithfully state 'This project uses Express 4.x, database is MongoDB.' What the agent reads isn't project documentation — it's a treasure map from 1923."

5. **Specific, memorable examples.** Use real technology names (Fastify, Postgres, pnpm, TypeScript), real company names (OpenAI, Anthropic, Chroma, Snyk), and absurdly specific scenarios ("夹杂着对当时 tech lead 的吐槽和对 Rust 的向往").

6. **Epigraph every technique.** Each `### N. Title` must be immediately followed by a blockquote with a satirical quote and attribution.

7. **"When caught, say" closer.** Every technique ends with `> When caught, say: "The excuse."` (English) or `> 被发现了就说："台词。"` (Chinese). The excuse must be something a real person might actually say — plausible corporate deflection.

8. **Shift tone mid-section.** Open with absurdity, then deliver genuine insight, then return to absurdity. Example: "Honestly, most projects' AGENTS.md doesn't need your deliberate poisoning. They're already outdated, contradictory, and bloated. The only thing you need to do is not fix them." — this is both a joke AND true.

9. **Cite real research/findings when relevant.** Mention real studies (Chroma's context degradation experiments, OpenAI's pattern reproduction findings, Snyk's security research) to ground the satire in reality. Even better: quote the source article's own words verbatim as the satirical anchor. When the original text is already absurd when read straight — Shihipar writing that agents "estimate colors with unicode characters," using 🟠 to mean `#FF6B35` — don't exaggerate. Just quote it. The reader laughs, then realizes you didn't make it up.

10. **Self-deprecating closure: passivity is indistinguishable from sabotage.** Many techniques end with a twist — "you probably don't need to sabotage anything, it's already broken." The canonical form: "You don't need to X. Just don't question why." This is the most devastating move in the arsenal. It tells the reader that the most terrifying scenario isn't someone maliciously breaking things — it's that nobody is breaking anything, and things are still this bad. Your inaction is weaponized. The reader laughs at the setup, then goes quiet at the twist.

11. **Show, don't argue — use story beats.** Don't write a paragraph that argues "X makes Y unreadable." Instead, tell a miniature story with characters, timeline, and an action chain. The architect opens the ASCII diagram on the morning commute → types "I'll review tomorrow" → tomorrow never comes → the failover bug lives in the diagram for six months → AZ1 goes down → post-mortem blames "the architecture doc didn't flag this." The story *is* the argument. No thesis statement needed. Every story beat adds another layer of satire — the reader assembles the meaning themselves.

12. **"When caught, say" must be a multi-layered corporate deflection cascade.** One excuse isn't enough — the best closer works like a real defensive email thread. Layer 1: appeal to industry standard ("Markdown is universal"). Layer 2: shift blame to culture ("if people aren't reading specs, that's a process problem"). Layer 3: weaponize scope creep ("switching to HTML means we'd need CSS standards and accessibility compliance"). Each layer deflects to a different stakeholder, and collectively they make any change feel like an irresponsible scope expansion. The reader should feel their inner bureaucrat nodding along.

### Chinese Translation Style

When translating to Chinese:
- Maintain the exact same deadpan tone — 一本正经地胡说八道
- Use colloquial Chinese ("你想想" / "说真的" / "更妙的是") not formal/academic Chinese
- Preserve all concrete examples and technology names
- Chinese epigraphs should feel like Chinese internet wisdom — sharp, cynical, quotable
- "被发现了就说" must deliver the same straight-faced corporate deflection

### What NOT to Do

- ❌ Emoji anywhere in the technique body
- ❌ Labels like "反转了啥" / "为什么有效" / "Why This Works"
- ❌ Numbered steps (1. 2. 3.) unless absolutely essential
- ❌ "This is satire" disclaimers
- ❌ Academic/scholarly tone
- ❌ Generic abstractions — always be specific
- ❌ Explaining why a joke is funny

---

## Insertion and Cross-References

See `AGENTS.md` § Cross-Reference Rules for insertion point, count update, layer assignment, defense objectives, and verification. Do not duplicate those rules here.

### Title Format

Titles should be:
- English: imperative, action-oriented, slightly absurd ("Turn AGENTS.md Into a Dumpster Fire", "Drown Tests in Documentation")
- Chinese: matching the same energy — colloquial, vivid, slightly absurd ("把 AGENTS.md 变成垃圾场", "用文档淹死测试")

---

## Example: Adding a Technique from Martin Fowler's "What Is Code?"

If the user provides `https://martinfowler.com/articles/what-is-code.html`:

1. Fetch: `curl -sL https://martinfowler.com/articles/what-is-code.html | w3m -dump -T text/html | head -500`
2. Find an insight: The essay discusses how code is communication between humans, not just instructions for machines. In the AI era, this becomes: agents treat code as machine instructions and ignore human intent.
3. Draft technique: "Write Code Only AI Can Understand" — remove all comments, use single-letter variables, rely entirely on the agent's "understanding" of the codebase.
4. Insert into both READMEs.
