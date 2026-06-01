# Add a New Technique

Follow this workflow when the reference describes a **failure-mode insight** — a general pattern, vulnerability, or anti-pattern in AI coding workflows that can be turned into a satirical technique.

## Write the Technique

Draft in English first, then translate to Chinese. Follow the style guide in the main `SKILL.md` (the **Writing Style — Deadpan Satire** section).

### Technique Structure

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

### Title Format

- English: imperative, action-oriented, slightly absurd ("Turn AGENTS.md Into a Dumpster Fire", "Drown Tests in Documentation")
- Chinese: matching the same energy — colloquial, vivid, slightly absurd ("把 AGENTS.md 变成垃圾场", "用文档淹死测试")

## Insert Into Both READMEs

### Insertion Point

In `README.md`, insert right before:

```markdown
## Alternative Angle: By Attack Surface
```

In `README-zh.md`, insert right before:

```markdown
## 换个角度：按攻击面
```

### Update Cross-References

After inserting technique N (where N = current count + 1):

1. **"N techniques above"** — In the first sentence of Alternative Angle: By Attack Surface, update the count.

2. **Layer references** — In the By Attack Surface section, each layer lists technique numbers. If the new technique fits a layer, add N to the list:
   - **Prompt Layer** — techniques about AGENTS.md, ADRs, docs, learning from mistakes
   - **Tool Layer** — techniques about hooks, config, permissions, feedback speed
   - **Context Layer** — techniques about context management, memory, history
   - **Orchestration Layer** — techniques about agent architecture, workflow, sub-agents

3. **What You're Protecting references** — If the new technique supports any of the four defense objectives (Make AI Code Unreviewable, Make AI Context Untrustworthy, Make the System Unsafe, Make the Boss Stop Believing in AI), add it to the relevant chain with `→`.

### Verify

Run `grep -c "^### [0-9]" README.md README-zh.md` to confirm both files have the same technique count. Check that all technique numbers in Alternative Angle sections are consistent.

## Example

Reference: `https://martinfowler.com/articles/what-is-code.html`

1. Insight: The essay discusses how code is communication between humans, not just instructions for machines. In the AI era → agents treat code as machine instructions and ignore human intent.
2. Draft technique: "Write Code Only AI Can Understand" — remove all comments, use single-letter variables, rely entirely on the agent's "understanding" of the codebase.
3. Insert into both READMEs, update cross-references.
