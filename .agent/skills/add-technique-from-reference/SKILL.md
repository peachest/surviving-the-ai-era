---
name: add-technique-from-reference
description: |
  Given a reference URL (article, blog, video, paper, incident report),
  fetch its content and add it to the ai-age-defensive-programming READMEs.
  Two modes: (1) failure-mode insight → new satirical technique in the
  Techniques section, (2) production disaster → new entry in Real-World AI
  Failures. Use when user provides a URL and says "add this," "新技法,"
  "add technique," "add incident," "add to README," or wants to expand
  the project from an external source. Also use for links about AI coding
  agents, harness engineering, prompt engineering, LLM safety, or
  agent-caused production outages.
---

# Add Technique From Reference

Add a new satirical technique to the [ai-age-defensive-programming](https://github.com/peachest/surviving-the-ai-era) READMEs (English + Chinese), inspired by a reference URL the user provides.

## Workflow

### Step 1: Fetch and Read the Reference

Fetch the URL content using tools in this priority order:

1. **Agent framework's built-in WebSearch tool** — if available, use it first. It handles fetching, rendering, and extraction automatically.
2. **Third-party search tools** — if the framework has an anySearch skill or similar web-fetch capability, use that.
3. **Fallback to `curl`** — only if no higher-level tool is available:
   - `curl -sL <url>` and pipe through `lynx -dump -stdin` or `w3m -dump` for HTML
   - For PDFs: download then `pdftotext`
   - For YouTube: note the title and description

Read the content and identify 1–3 interesting insights about:

- AI coding agents (Claude Code, Cursor, Copilot, etc.)
- Agent harness engineering (hooks, sandboxing, context management)
- Prompt engineering failures
- LLM reliability or safety issues
- Developer workflows with AI
- Production incidents involving AI

The insight must be a **real failure mode** — something that can plausibly go wrong. Satire only works when the kernel is true.

### Step 2: Classify — Technique or Incident?

Determine what kind of content the reference describes:

**A. Failure-mode insight** — The reference discusses a general pattern, vulnerability, or anti-pattern in AI coding workflows (e.g., context degradation, over-feedback, self-modifying config). These become new satirical techniques.
→ Go to **Step 3a: Add a New Technique**.

**B. Real-world production disaster** — The reference documents a specific, named incident where an AI agent or LLM caused actual damage to a real organization (e.g., deleted databases, leaked secrets, production outages). These go into the Real-World AI Failures section.
→ Go to **Step 3b: Add to Real-World AI Failures**.

If the reference covers both (a disaster that also reveals a general pattern), add to both sections — the incident first, then optionally a technique that generalizes it.

---

### Step 3a: Add a New Technique

Write the technique following the exact structure and style below. Write English first, then translate to Chinese.

**Insertion point:** Right before the `## Alternative Angle: By Attack Surface` section (English) / `## 换个角度：按攻击面` section (Chinese).

After insertion, update:

1. **Technique numbering** — the new technique gets the next sequential number (currently 10, so new = 11).
2. **"The ten techniques above"** → "The eleven techniques above" in the Alternative Angle: By Attack Surface section (and its Chinese equivalent).
3. **Reference counts in the Alternative Angle sections** (e.g., `Techniques 1, 3, 5, 6, 9` — add the new technique number if it fits a layer).
4. **Numbered references in What You're Protecting** section if the new technique is relevant (e.g., `Drown tests in docs (Technique 5)` ).

---

### Step 3b: Add to Real-World AI Failures

The `## Real-World AI Failures` section has two sub-sections. Classify the incident:

- **`### Production Incidents`** — General AI/LLM failures: chatbot hallucinations, data leaks, research findings about AI misbehavior. NOT specifically about coding agents.
- **`### Agent-Caused Production Destruction`** — Specific incidents where an AI *coding agent* (Claude Code, Cursor, Copilot, Replit AI, etc.) caused production damage through its own actions.

Each entry follows this exact format:

**English** (`README.md`):

```markdown
- **[Title describing the incident](url)**
  (YYYY-MM) — Who/what happened. Key detail. Scale of damage.
  Consequence or follow-up action.
```

**Chinese** (`README-zh.md`):

```markdown
- **[中文标题，描述事件](url)**
  （YYYY-MM）——谁/发生了什么。关键细节。损害规模。
  后果或后续行动。
```

**Style rules for incidents:**

- Title is the link text — punchy, factual, names the agent/tool involved
- Date in `(YYYY-MM)` format — if only year is known, use `(YYYY)`
- Body: 2–3 sentences. First sentence = what happened. Second = why/how. Third = consequence.
- No satire in the incident body — these are factual records, not jokes. The humor comes from the accumulated horror of reading them all.
- Only the epigraph at the top of the section can be satirical.

**Insertion point:** At the end of the appropriate sub-section, before any closing line like `*Know of another incident?*`.

After adding, verify both READMEs have the same incidents in the same order.

---

### Step 4: Verify Cross-Links

For techniques: run `grep -c "^### [0-9]" README.md README-zh.md` to confirm both files have the same technique count. Check that all technique numbers in Alternative Angle sections are consistent.

For incidents: visually confirm both files list the same incidents in the same order under each sub-section.

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

2. **Never explain the joke.** No emoji, no "this is satire" disclaimers in the technique body. The absurdity must stand on its own. The reader should only realize it's satire halfway through.

3. **No numbered lists.** Sub-sections flow as narrative paragraphs. Use bold lead-ins (**First, length.** / **Second, content.** / **Never update.**) — never `1.` `2.` `3.`.

4. **Concrete to the point of absurdity.** Don't say "add outdated information." Say "The project migrated from Express 4 to Fastify, from MongoDB to Postgres, from a monolith frontend into seven micro-frontends — but your AGENTS.md should faithfully state 'This project uses Express 4.x, database is MongoDB.' What the agent reads isn't project documentation — it's a treasure map from 1923."

5. **Specific, memorable examples.** Use real technology names (Fastify, Postgres, pnpm, TypeScript), real company names (OpenAI, Anthropic, Chroma, Snyk), and absurdly specific scenarios ("夹杂着对当时 tech lead 的吐槽和对 Rust 的向往").

6. **Epigraph every technique.** Each `### N. Title` must be immediately followed by a blockquote with a satirical quote and attribution.

7. **"When caught, say" closer.** Every technique ends with `> When caught, say: "The excuse."` (English) or `> 被发现了就说："台词。"` (Chinese). The excuse must be something a real person might actually say — plausible corporate deflection.

8. **Shift tone mid-section.** Open with absurdity, then deliver genuine insight, then return to absurdity. Example: "Honestly, most projects' AGENTS.md doesn't need your deliberate poisoning. They're already outdated, contradictory, and bloated. The only thing you need to do is not fix them." — this is both a joke AND true.

9. **Cite real research/findings when relevant.** Mention real studies (Chroma's context degradation experiments, OpenAI's pattern reproduction findings, Snyk's security research) to ground the satire in reality.

10. **Self-deprecating closure.** Many techniques end with a twist — "you probably don't need to sabotage anything, it's already broken." This creates the meta-layer: the satire describes real failures that happen without malice.

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

## Insertion Mechanics

### Finding the Insertion Point

In `README.md`, insert right before this line:

```markdown
## Alternative Angle: By Attack Surface
```

In `README-zh.md`, insert right before this line:

```markdown
## 换个角度：按攻击面
```

### Updating Cross-References

After inserting technique N (where N = current count + 1):

1. **"N techniques above"** — In the first sentence of Alternative Angle: By Attack Surface, update the count.

2. **Layer references** — In the By Attack Surface section, each layer lists technique numbers. If the new technique fits a layer, add N to the list:
   - **Prompt Layer** — techniques about AGENTS.md, ADRs, docs, learning
   - **Tool Layer** — techniques about hooks, config, permissions, feedback
   - **Context Layer** — techniques about context, memory, history
   - **Orchestration Layer** — techniques about agent architecture, workflow

3. **What You're Protecting references** — If the new technique supports any of the four defense objectives (Make AI Code Unreviewable, Make AI Context Untrustworthy, Make the System Unsafe, Make the Boss Stop Believing in AI), add it to the relevant chain with `→`.

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
