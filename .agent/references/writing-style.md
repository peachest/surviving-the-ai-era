# Writing Style — Deadpan Satire

> Modeled on Roedy Green's *How to Write Unmaintainable Code* — absurd advice delivered with complete seriousness. The reader should only realize it's satire halfway through.

## Technique Structure

```markdown
### N. Title (English)

> *"Epigraph — a satirical one-liner."*
> — Attribution (can be fictional, absurd, or real)

[Opening: set up what "smart people" do, then "We are not smart people" /
"We do the opposite." This establishes the inverted world.]

[Body: 2–5 sub-sections, each starting with a bold phrase. No 1. 2. 3.
numbering. Use conversational leads: **Downscale? Why would you.** /
**压什么缩。** Avoid **First, …** / **首先** — they kill the deadpan tone.]

[Closing: a story-beat twist — the reader arrives at "passivity did this"
through narrative, not a template sentence. No "说真的，你可能不需要…" fill-in.]

> When caught, say: "The excuse — multi-layered corporate deflection cascade."
```

## Core Rules

1. **Second-person, conversational.** "You" / "we" / "your boss." Talk like a jaded senior dev corrupting a junior over drinks. Never third-person academic ("the agent fails to distinguish").

2. **Never explain the joke.** No emoji, no "this is satire" disclaimers, no meta-commentary ("Here's the thing," "说白了"). The absurdity must stand on its own.

3. **No numbered lists.** Bold phrase lead-ins each section. Chinese **首先** / **其次** / **然后** is fatal — reads as a textbook recitation. Use conversational absurd leads: **压什么缩。** / **先看图，后看字。** / **坐标为什么要缩放。**

4. **Concrete to absurdity.** Don't say "add outdated info." Say "The project migrated from Express to Fastify, MongoDB to Postgres — but your AGENTS.md should faithfully state 'uses Express 4.x, database MongoDB.' A treasure map from 1923."

5. **Real names, real numbers.** Fastify, Postgres, pnpm, Anthropic, Chroma, Snyk. Specific resolutions (5120×2880, 1.15MP), specific counts (17 failures, 47 tests, 4 audits, 0 product launches).

6. **Epigraph every technique.** Blockquote + attribution immediately after the title.

7. **"When caught, say" closer.** Multi-layered corporate deflection cascade:
   - Layer 1: appeal to industry standard ("Markdown is universal")
   - Layer 2: shift blame to culture ("if people aren't reading specs, that's a process problem")
   - Layer 3: weaponize scope creep ("switching to HTML means we'd need CSS standards and accessibility compliance")
   Each layer targets a different stakeholder. The reader's inner bureaucrat should nod along.

8. **Shift tone mid-section.** Absurdity → genuine insight → absurdity. "This is both a joke AND true."

9. **Cite real research.** Chroma's context degradation experiments, OpenAI's pattern reproduction findings, Snyk's 36–40% vuln rate. Quote the source verbatim when the original is already absurd — Shihipar writing that agents "estimate colors with unicode characters," using 🟠 to mean `#FF6B35`. Don't exaggerate. Just quote it.

10. **Passivity is sabotage — but don't reuse the template.** The self-deprecating twist ("the system is already broken, you just need to do nothing") is devastating. But "说真的，你可能不需要…" / "Honestly, you don't need to…" cannot be a Mad Libs closer — if every technique ends with this sentence, the reader stops feeling the twist and starts pattern-matching. Deliver the same insight through a **story beat**: a timeline ("three months later, the boss approves more budget"), a scene ("the boss flips through screenshots, sees the agent clicking in the air"), a reveal ("what the boss can't see is that every screenshot was silently degraded"). The reader arrives at "passivity did this" on their own, through narrative.

11. **Show, don't argue.** No thesis statements. Tell a miniature story with characters, timeline, and action chain. Example: architect opens ASCII diagram on commute → types "I'll review tomorrow" → tomorrow never comes → the failover bug lives in the diagram for six months → AZ1 goes down → post-mortem blames "the architecture doc didn't flag this." The story *is* the argument. No explanation needed.

12. **Chinese: 一本正经地胡说八道.** Colloquial, sharp, cynical — never formal/academic. Epigraphs should feel like Chinese internet wisdom. **被发现了就说** must deliver the same straight-faced corporate deflection as the EN version. Preserve all concrete examples and technology names.

## Never Do

- ❌ Emoji in technique body
- ❌ "Why This Works" / "反转了啥" labels
- ❌ Numbered steps (1. 2. 3.)
- ❌ "This is satire" disclaimers
- ❌ Academic tone
- ❌ Generic abstractions — always be specific
- ❌ Explaining why a joke is funny
