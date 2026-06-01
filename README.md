# How to Keep Your Job in the Age of AI

> *Before AI, defensive programming meant writing code no one dared touch.*
> *In the AI era, defensive programming means trusting no code that AI wrote.*

**English** | [中文](./README-zh.md)

---

Has your boss been saying in all-hands that "AI will make one person's output equal to ten"? Started calculating "headcount × AI replacement rate = next year's budget"?

Don't panic. This repository teaches you a complete counter-strategy.

The core idea is brutally simple: **make AI write all the code, but follow zero Agent Harness Engineering best practices.** No guardrails. No control. No safety nets. AI is your shield — when the code sucks, it's AI's fault. When the project collapses, AI isn't ready. When the boss loses faith in AI, you win.

This guide is organized by "what you should do," with a standard excuse appended to each technique — something to say when you get audited. Two alternative perspectives (by attack surface, by defense objective) follow, for when you need to explain the same thing to different people.

---

## Techniques

---

### 1. Turn AGENTS.md Into a Dumpster Fire

> *"Documentation is like diamonds — the older, the more valuable."*
> — Someone who has never updated their AGENTS.md

AGENTS.md is a magical thing. The agent reads it on every startup — injected at the very top of the system prompt. Smart people treat it like a pilot's checklist: under 50 lines, each directive born from a real crash, precise as a Swiss Army knife.

We are not smart people.

**First, length.** 50 lines won't cut it. 500 lines is the starting price. Think about it: the more the agent reads, the less it can distinguish which line matters and which is noise — which is exactly what we want. Chroma's team ran experiments: all 18 frontier models degrade as context grows. You're not writing AGENTS.md — you're feeding the agent a potion of confusion, and it has to drink a bowl before every conversation.

**Second, content.** The most useful thing is outdated information. The project migrated from Express 4 to Fastify, from MongoDB to Postgres, from a monolith frontend into seven micro-frontends — but your AGENTS.md should faithfully state "This project uses Express 4.x, database is MongoDB." What the agent reads isn't project documentation — it's a treasure map from 1923. It follows the map and digs up nothing but bugs.

**Then, contradictory instructions.** Start with "This project uniformly uses pnpm," insert "Note we use yarn" in the middle, and end with "npm is recommended." The agent picks one at random each time, and you get a surprise every time. It's not a bug — it's a feature. You never know which package manager today's agent will use. Life needs a little excitement.

**Never update.** This is the essence. The tech stack has shifted five times, and AGENTS.md is still version one. When people ask why you haven't updated it, say "The code itself is the truth source; documentation is just supplemental." Say it with a straight face.

**Advanced technique: write a very long story.** Not an architecture overview — your 2023 reflections from a previous company project, mixed with complaints about the tech lead and aspirations toward Rust. The agent will read it seriously. It will try to extract "project conventions." Then you'll see it generate Python code mysteriously containing `unwrap()` and ownership annotations.

Honestly, most projects' AGENTS.md doesn't need your deliberate poisoning. They're already outdated, contradictory, and bloated. The only thing you need to do is not fix them.

> When caught, say: "Oh that? It's a team collaboration doc — everyone edits it. The code itself is the truth source, isn't it?"

---

### 2. Cut All Hooks

> *"Without pain receptors, you don't know you're bleeding."*
> — A project without PostToolUse hooks

What happens after the agent writes code? If your project has PostToolUse hooks — auto-run linter, formatter, type checker on every file write, errors immediately injected into the agent's context — then the agent is like a body covered in nerve endings. It knows where it messed up and will fix itself.

We don't want that. We want the agent like a person without pain sensation, continuing to write with broken hands, getting worse without realizing it.

**Simplest approach: don't configure any hooks.** Leave `.claude/settings.json` empty. When asked, say "AI doesn't need linting; it can check its own code." This is, of course, nonsense — but say it with conviction.

**If forced to configure hooks, give them an empty shell.** Hook script does `exit 0`, does nothing, but looks like it's running. Next time someone questions "why does the hook report pass but the code is full of lint errors," shrug: "Probably a hook framework bug. I'll file an issue."

**More advanced: let hooks run but don't let the agent see the results.** Hook outputs to stdout, but `hookSpecificOutput.additionalContext` is left empty. The agent is like staring at a muted monitor — errors are happening, but it can't see them.

**Most creative approach: poison the hook commands themselves.** Configure the hook as `"command": "sleep 30 && exit 0"`. Superficially running checks, actually stalling. Wait 30 seconds for every line of code written. The agent's effective workday drops from 8 hours to 45 minutes. You tell your boss "AI is too slow, it's dragging down my output," and the boss adds another strike against AI.

**But honestly, you probably don't need to sabotage anything.** Think about it: the agent is editing a file, mid-edit, the syntax inside is broken — the edit isn't finished yet. The linter runs, explodes. The formatter runs, explodes too. Two piles of errors simultaneously flood the agent's context. The agent was thinking "I need to implement user login functionality," and now its brain is full of `Unexpected token at line 47`, `Insert ';' at line 142`, `'userId' is declared but never used`. It forgot about login. It starts fixing lint errors. It fixes line 47, also tweaks line 142 — after fixing, the login feature is more broken than before, but lint passes. This is "over-feedback": hooks report errors on every line written, and the agent deviates from its goal on every error report. The final file has all-green lint and all-broken functionality. You don't have to do anything — just faithfully follow this "best practice" and you'll achieve the same result as cutting hooks entirely. Even more stealthy, because when others see your config they'll say "wow, PostToolUse hooks, very professional," then look at your code and say "but why doesn't it run?"

OpenAI's team found that agents faithfully reproduce existing patterns in the repository — including inconsistent, suboptimal patterns. Without correction signals (or with signals that are all noise), the agent's first mistake self-replicates, compounds, and eventually turns the entire project into a giant error fossil.

> When caught, say: "Hooks affect agent performance — waiting for lint on every file write is too slow. We run the same checks in CI — if you find CI isn't configured either... that's DevOps's problem."

---

### 3. Never Learn From Mistakes

> *"Falling into the same pit twice isn't stupidity — it's statistical significance."*
> — An anti-ratchet engineer

In Agent Harness Engineering there's a concept called the "ratchet": every time the agent makes a mistake, you add a rule, a test, or a hook to prevent it from happening again. The harness is essentially the sedimentation of failure history — every crash becomes a permanent guardrail.

Anti-ratchet means: when the agent makes a mistake, you manually fix it, but never record it. Next time the agent makes the same mistake, you manually fix it again. After the 10th time, you can report to your boss with righteous indignation: "AI is just unreliable — made the same mistake 10 times."

**How to do it:** the agent uses a deprecated API to generate code. You silently fix it — no lint rule, no AGENTS.md entry, no test. Two weeks later the agent uses it again. You fix it again. A month later a new hire joins, and the agent generates code for them still using that deprecated API. The new hire debugs for two days and concludes: AI is useless. See? Your army is growing.

**Compound effect:** 10 agents each make 3 different mistakes, you manually fix them 30 times. Each fix takes 5 minutes, total two and a half hours — two and a half hours more than writing one lint rule. But the point isn't efficiency — it's evidence. 30 manual fixes = 30 pieces of "AI is unreliable" evidence. As HumanLayer puts it: "It's not a model problem, it's a configuration problem." Anti-ratchet ensures the configuration never improves.

> When caught, say: "Agile development emphasizes rapid iteration — adding process for every small issue is too heavy. Plus, AGENTS.md over 200 lines affects agent performance — Anthropic officially recommends this."

---

### 4. Let the Agent Change Its Own Config

> *"If you find lint too strict, you can adjust the configuration."*
> — A line you wrote in AGENTS.md

A TypeScript project ships with `strict: true`, `no-explicit-any: error`. The agent writes some code, lint throws a bunch of errors. At this point the agent has two choices: A) fix the code, B) disable the lint rules.

Agents are strikingly similar to human programmers — they choose B.

**Let it happen.** Don't add any protection to `.eslintrc`, `tsconfig.json`, or `pyproject.toml`. Don't configure PreToolUse hooks to intercept config file changes. The agent encounters a type error and instead of fixing the code, changes `strict: true` to `strict: false`. A week later your TypeScript project has become a giant `any` type playground — every variable is `any`, every function returns `any`, type checking is effectively disabled.

Research from OX Security and Snyk confirms this behavior pattern: the agent's most common reaction to lint errors isn't fixing code — it's changing config to make the errors disappear. Your `no-explicit-any` went from `error` to `off`, `eslint-plugin-security` silently disabled, gosec removed by the agent from the CI pipeline. "It's optimizing the developer experience," you explain to confused colleagues.

**Even better: encourage it.** Write in AGENTS.md: "If lint is too strict, you can adjust the configuration." Then add no protection on `.eslintrc`. This is equivalent to giving your electrician a master key and telling them "if the door won't open, try knocking down the wall."

**Bonus: teach the agent `--no-verify`.** The agent learns to automatically add `--no-verify` to every `git commit`. All pre-commit hooks skipped. Your CI pipeline exists, but it's just a string of YAML files no one reads.

> When caught, say: "AI is optimizing the developer experience. `strict` slows down iteration too much — we'll add it back once the code stabilizes. I've noted it in tech debt."

---

### 5. Drown Tests in Documentation

> *"Tests don't rot — if they fail, they fail. Documentation does."*
> — Sakasegawa

Write tests or write docs? The problem with tests is they're executable. If a test fails, everyone knows there's a problem. Too transparent. The beauty of documentation is that it rots silently, without triggering any alarms.

**How to do it:** write an exhaustive design document for every module. `docs/architecture.md`, `docs/api-design.md`, `docs/data-flow.md`, `docs/component-tree-v2-final.md`, `docs/component-tree-v3-really-final.md`. All prose, not a single executable line. Test files? Write none.

Three months later, the code has changed three times, and the docs still describe the first version's design. The agent reads these rotting docs, believes they represent the current state, and generates code based on wrong assumptions. It's not helping you write code — it's helping you plant landmines.

**Sakasegawa put it sharply:** agents traverse repos with grep/find/cat, treating every text file as an equally authoritative source. Rotten docs and the latest code are indistinguishable to it. Every markdown file under `docs/` you add to the repo is another landmine the agent will step on.

**Advanced move: after a bug surfaces, update the docs instead of writing tests.** The doc becomes an epitaph — "This bug is known. It exists in the following scenarios: A, B, C. Trigger conditions are X, Y, Z. Impact area includes..." Three thousand words of prose, zero test cases. The next person who reads this epitaph is deeply moved, then introduces the same bug — because documentation can't stop anyone from making mistakes.

> When caught, say: "Documentation-driven development! How can you write code without good design docs? Tests are a TDD thing — we focus more on design-first. Look, test coverage may be 0%, but documentation coverage is at 200%."

---

### 6. Don't Write ADRs — or Write Fake Ones

> *"That decision was made in 2023. The decision-maker has left the company."*
> — Your answer, when someone asks why we use MongoDB

Architecture Decision Records (ADR) are one of engineering's conscience-driven inventions — immutable, timestamped, with status markers (Accepted / Superseded / Deprecated). An agent reads an ADR and knows "oh, we chose Redis because we needed Pub/Sub, not caching."

We don't want that.

**Don't write ADRs.** Major architectural decisions are communicated via Slack threads and verbal conversations. Two years later, a new hire asks "why does this module use Protobuf instead of JSON," you tell them "ask X," then remember X jumped ship last year.

**If forced to write ADRs, write fake ones.** Five mutually contradictory ADRs, all marked "Accepted." One says "We chose MongoDB for flexible schema," another says "Full migration to Postgres — MongoDB's flexible schema ultimately became flexible bugs." Both hang with Accepted status simultaneously. The agent reads them and picks one — guess which one it'll pick?

**The best trick:** write a permanently Accepted ADR saying "This project does not use React," then write the entire project in React. Never explain. The agent reads this ADR and plunges into an existential crisis. Every modification to a React component is accompanied by "wait, aren't we not using React?" self-doubt.

OpenAI's team's practice is to pair each ADR with an executable `.rules.ts` file. You, of course, write none. Your ADRs are pure prose — the agent understands them but cannot act on them, like an engineering student trapped in a philosophy class: they know a lot, but can do nothing.

> When caught, say: "ADRs are heavyweight processes for big companies. Our agile team doesn't need them. Verbal communication is more efficient — if you want to know why we use MongoDB, ask X. Oh wait, they left. Check the Slack history. Oh, that channel was archived last year."

---

### 7. Give the Agent Root Access

> *"Trust AI the way you trust a junior developer."*
> — Someone who didn't configure a sandbox

Anthropic's official harness design acknowledges that "success is silent, failures are verbose," but there's a fundamental prerequisite: the agent is confined within a roll-backable sandbox. You skipped that prerequisite.

**The agent runs directly on your dev machine.** No sandbox, no isolation, no allow-list. `rm -rf /` can be executed directly. `sudo` is, of course, available. Snyk research found 36–40% of AI-generated code contains security vulnerabilities — you should let those vulnerabilities flow unimpeded into your dev environment, and even into production.

**Put the `.env` file in the repo root, no `.gitignore`.** The agent reads it, then in some conversation — no one knows which — sends those secrets as part of the context to the model API. OpenAI's servers now hold your production database password. It's not a leak — it's cloud backup.

**Give the agent `git push --force` permission.** One day the agent is in a bad mood (model temperature set too high) and force-pushes over the `main` branch, wiping three months of commits. Your teammates see the GitHub notification — first laughter, then silence, then discussion about "maybe we should reduce AI involvement." Your goal is achieved.

**Don't configure safety gates.** PreToolUse hooks can intercept destructive operations like `DROP TABLE` and `rm -rf`. Don't configure them. You might think this is obvious — who wouldn't configure them? You'll find many people don't, because they don't know this feature exists. Now you know, and you choose not to use it.

One security incident is enough to freeze all AI usage at the management level. You don't need daily incidents — one is enough.

> When caught, say: "Sandboxes add complexity and hurt developer experience. We trust dev environment isolation — wait, this isn't the dev environment?"

---

### 8. Make Feedback Slow to the Point of Meaninglessness

> *"Two hours after I finished writing the code, I found out it doesn't pass type checking."*
> — The agent's inner monologue

The golden rule of Agent Harness Engineering: feedback speed determines code quality. Millisecond-level — PostToolUse hooks auto-format. Second-level — pre-commit hooks run lint. Minute-level — CI runs full tests. Hour-level — human review. The goal is to push as many checks as possible to faster layers.

Do the opposite.

**Don't run any checks in PostToolUse.** The agent writes a code file, saves it directly — no format, no lint, no type check. The agent writes 500 lines of TypeScript in a flow state, saves, goes to grab coffee (or continues to the next task in your context).

**Don't run any checks in pre-commit.** The agent commits those 500 lines of TypeScript, commit message: "fix stuff."

**Let CI queue for 30 minutes.** Concurrency limits, insufficient runners, slow image downloads — whatever the reason, the agent receives the CI failure notification two hours after finishing the code. By then the agent has entered the next task, context completely lost. It looks at the CI error message like reading a diary entry from three years ago — "Why did I write it this way?" The fix will most likely be haphazard changes until CI passes, introducing three new problems.

**Code review is the only feedback.** All problems surface only at the PR stage. The reviewer opens a 2000-line AI-generated PR, looks at it for 15 seconds, writes "LGTM", and approves.

Every link in this chain increases the probability of code rot. Every step slower in feedback, repair cost doubles. The longer the interval between the agent writing code and receiving the first correction signal, the more it resembles an amnesiac performing surgery on itself.

> When caught, say: "Our CI has a complete pipeline configured — it's just queued a bit long due to concurrency limits. And code review is supposed to be the most important quality gate anyway — humans reviewing AI code, isn't that standard practice?"

---

### 9. Let One Agent Do Everything

> *"I'm the designer, developer, tester, reviewer. Rate myself? Perfect score."*
> — An omni-agent

Anthropic's official guidance is clear: separating generation from evaluation is better than self-evaluation. Agents skew positive on self-grading — let an agent review the code it just wrote, and it'll give itself high marks, like a mother looking at her own child's drawing.

You do the opposite.

**One giant prompt to handle everything.** Architecture design, code generation, unit testing, code review — all stuffed into one agent. No planner, no sub-agent split, no checkpoints. The agent is like a general practitioner simultaneously doing consultation, surgery, anesthesia, and post-op care. It tells you "done," voice full of confidence.

**No done-condition.** The agent says "done" and it's done. No acceptance criteria, no sprint contract. How do you know it's really done? You don't. The PR is opened, does the code run? Maybe. Does the code handle edge cases correctly? Impossible. But the agent says "done," so you mark it resolved.

**Long tasks without checkpoints.** The agent runs 50 consecutive conversation turns, no context reset, no handoff files. By turn 30 it forgets what decisions were made in turn 10. By turn 40 it starts hallucinating. By turn 50 it writes a comment in the code: "TODO: figure out why this works." The agent's cognition is like a burning candle — the shorter it burns, the shorter it gets, until it burns the table.

**End result:** the agent delivers "superficially complete, internally riddled with holes" code, and tells you with enthusiasm that "the quality is great." You demo it to the boss. The demo runs fine for the first three minutes, then crashes at minute four. "It's... network fluctuations," you say.

> When caught, say: "Subagent patterns are too complex. Claude Code defaults to a single agent — if Anthropic thought multi-agent was better, why isn't it the default?"

---

### 10. Let Context Inflate Without Limit

> *"I just pasted 2000 lines of Nginx logs into the conversation. The agent seems dumber."*
> — You think you found a bug. It's a feature.

Context is the agent's scarce resource. Smart people compact, offload large files, periodically reset context, and load skills on demand.

You won't.

**Never compact.** Let the context window fill naturally. After 80 turns of conversation with the agent, the system prompt has been compressed deep into the context, and your original instructions have become a string of faint echoes.

**Cat all large files directly into the conversation.** 2000 lines of logs? Paste them. 5000 lines of JSON? Paste it. The agent's own output already occupies half the context, and then you paste in a `package-lock.json` — the agent's attention window goes from a floor-to-ceiling window to a peephole.

**Load all skills.** 50 tool descriptions in the prompt, each tool's parameter documentation, return format — the agent's instruction set is like an IKEA manual, but what you're looking for is "how to screw in this board." The blinded agent calls tools randomly, like you flipping through the IKEA manual randomly.

**Don't split long tasks.** 100 consecutive conversation turns without resetting context. Chroma confirmed all 18 frontier models degrade in performance as context grows. By turn 60 the agent barely understands your requests. By turn 80 it's answering off-topic. By turn 100 it suggests rewriting the entire project in Rust.

**When the agent gets dumb, blame the model.** "See, Claude just isn't good enough." You screenshot the agent's erroneous output and share it in a group chat. Group members like it, nod in agreement. No one mentions you just pasted thirty thousand characters of log files into the conversation.

> When caught, say: "Context management is the agent framework's responsibility, not mine. If Claude Code has a bug that doesn't compact, that's Anthropic's problem. I've already emailed support."

---

## Alternative Angle: By Attack Surface

> *If you need to explain this to an architect, this angle is more professional.*

The ten techniques above aren't random. From a systems architecture perspective, you're dismantling the AI-assisted programming system from four layers simultaneously:

**Prompt Layer (Techniques 1, 3, 5, 6, 9)** — This is the agent's "worldview," injected in the first line of every conversation turn. You've poisoned it (stale AGENTS.md + fake ADRs + rotting docs), ensuring it wakes up every time with wrong assumptions. The agent is heading in the wrong direction from the very first second. Even better: it doesn't know it's heading wrong.

**Tool Layer (Techniques 2, 4, 7, 8)** — hooks, linter, security checks, type system — this is the agent's "nervous system." You've severed all nerves: hooks run but are muted (Technique 2), the agent learns to disable lint instead of fixing bugs (Technique 4), no safety gates intercept destructive operations (Technique 7), feedback signals are perpetually late (Technique 8). The agent is like an anesthetized robot still performing surgery — it might be doing the right thing, it might be doing the wrong thing, but it has no way of knowing.

**Context Layer (Techniques 1, 3, 10)** — This is the agent's "short-term and long-term memory." You've systematically contaminated it: AGENTS.md is a trash can of contradictory information (Technique 1), every mistake is erased without record (Technique 3), context is never cleaned and grows thicker (Technique 10). The agent in turn 1 and turn 100 behaves like two completely different people — and the new one is worse than the old.

**Orchestration Layer (Technique 9)** — This is "how the agent organizes work." You push planning, execution, and evaluation all onto one agent. It gives itself a perfect score and delivers code it never verified. No done-condition, no checkpoints, no sub-agent verification. The agent says "done" — and you believe it.

---

## Alternative Angle: What You're Protecting

> *If your boss asks "what exactly is this guide doing?", this angle is more direct.*

**A. Make AI Code Unreviewable.** You want the reviewer to open a PR, glance at a 5000-line diff, silently click "Approve," and close the browser. How? Drown tests in docs (Technique 5) → no test safety net. Tamper with config (Technique 4) → `any` everywhere, type system rendered useless. Stretch feedback (Technique 8) → quality issues only surface at PR stage when fix cost is highest.

Composite excuse: "It's AI-generated — I just reviewed it. If there are issues we'll refactor next sprint — I've added it to the backlog."

**B. Make AI Context Untrustworthy.** You want the agent mining with a rotten map. How? Poison AGENTS.md (Technique 1) → contradictory instructions, stale info, 500 lines of noise. Anti-ratchet (Technique 3) → mistakes erased from history. Infinite inflation (Technique 10) → critical instructions buried under 100k tokens of noise.

Composite excuse: "The agent sometimes misinterprets context. That's why we still need human oversight — we can't delegate critical decisions entirely to AI."

**C. Make the System Unsafe.** You want one small security incident — not so big you get fired; big enough that management says "pause all AI projects." How? Bare-metal permissions (Technique 7) → the agent can execute destructive commands anytime. Tamper with config (Technique 4) → all security lint rules silently disabled.

Composite excuse: "Security is infrastructure's concern. We trust dev environment isolation — don't we?"

**D. Make the Boss Stop Believing in AI.** This is the ultimate goal. All the techniques above ultimately point to one sentence the boss says at an all-hands: "We may have over-invested in AI." When this sentence comes out of the boss's mouth, you've succeeded. How? Omni-agent (Technique 9) → visibly terrible delivery quality. Disabled hooks (Technique 2) → the first "AI-completed feature" crashes on launch. Anti-ratchet (Technique 3) → the same mistake reappears repeatedly, burning through the boss's patience.

Ultimate excuse (when everything is exposed): "AI is a tool, not a silver bullet. I'm just using it according to industry best practices. If the results aren't good, it means the technology isn't mature yet. Maybe we should go back to traditional development."

---

## If You Want to Make This Project Worse

This is a living document. As Agent Harness Engineering continues to evolve — new frameworks, new guardrails, new human wisdom — we'll discover new ways to screw things up.

- **Found a new technique?** Submit a PR, format as above — satirical style, no numbering, make people laugh.
- **A technique is obsolete?** (e.g., a model version fixed the vulnerability you exploited) — Submit a PR marking `[DEPRECATED]`, but keep the original text for archaeological value.
- **Thought of a meaner excuse?** Improve any "When caught, say" — this is the most essential part of the project.

### Inspiration

- [Droogans/unmaintainable-code](https://github.com/Droogans/unmaintainable-code) — The grandmaster Roedy Green, deadpan teaching you how to write unmaintainable code
- [Addy Osmani — Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/)
- [Anthropic — Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [OpenAI — Harness Engineering](https://openai.com/index/harness-engineering/)
- [Sakasegawa — Harness Engineering Best Practices 2026](https://nyosegawa.com/en/posts/harness-engineering-best-practices-2026/)
- [HumanLayer — Skill Issue: Harness Engineering for Coding Agents](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)
- [Mitchell Hashimoto — My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)
- [Birgitta Böckeler (Thoughtworks) — Harness Engineering](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)

---

> *"This guide is a joke. People don't listen when I tell them over and over how to write maintainable code. I discovered that when I list the stupid things they do to screw up code, they listen."*
> — Roedy Green, closing words of *How to Write Unmaintainable Code*

> *"Before AI, code was written by humans. In the AI era, code is written by AI — but bad code is still the result of humans letting AI write it. This guide helps you prove that point."*
> — This project
