# Agent & AI Coding References

> Curated links about AI coding agents, prompt engineering, LLMs, and the craft of software in the AI era.
> Categorized by type. Each entry includes a URL and a one-sentence description.

---

## Websites

- **[What Is Code?](https://martinfowler.com/articles/what-is-code.html)** — Martin Fowler's 2026 essay on code's dual purpose as machine instruction and domain conceptual model; foundational for understanding what LLMs commoditize vs. what they can't replace.
- **[The VibeSec Reckoning](https://martinfowler.com/articles/vibesec-reckoning.html)** — Thoughtworks engineers expose how vibe coding creates systemic security exposure; agents prioritize path of least resistance, prompting "be secure" is not enforcement, harness engineering with Guides + Sensors as the fix.
- **[Playwright+TDD：让AI写出真正能用的代码](https://mp.weixin.qq.com/s/QRFnt4pSB38EOBCC85j3tQ)** — Microsoft/GitHub dev advocate Marlene Mhangami at AI Engineer 2026: AI writes self-confirming tests (tests that validate implementation, not spec), TDD revived by AI in red-green-refactor loop, Playwright for behavior-driven testing over unit tests.
- **[为什么 Claude Code 在大项目里就拉胯了？](https://mp.weixin.qq.com/s/3jae7U90W6gBhYEBsmGaog)** — Analysis of Claude Code on large codebases: the ceiling isn't the model but the harness (CLAUDE.md, Hooks, LSP, Skills, Subagents); without proper infrastructure the best model gets lost; 1M context needs a good starting point.
- **[How Claude Code Works in Large Codebases: Best Practices and Where to Start](https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start)** — Anthropic's official guide (May 2026): the harness (CLAUDE.md, hooks, skills, plugins, MCP, LSP, subagents) matters as much as the model; three deployment patterns for making large codebases navigable at scale.
- **[Using Claude Code: The Unreasonable Effectiveness of HTML](https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html)** — Claude Code team member Thariq Shihipar (May 2026): HTML beats Markdown as agent output format — richer information density, visual clarity, shareability, and human-in-the-loop engagement; Markdown keeps humans out, HTML keeps them in.
- **[Best Practices for Computer and Browser Use with Claude](https://claude.com/blog/best-practices-for-computer-and-browser-use-with-claude)** — Anthropic's official guide (May 2026): screenshot resolution/scaling is the single highest-impact optimization for click accuracy; thinking effort tuning (medium is sweet spot for 4.6, high for Opus 4.7); prompt injection classifiers run automatically with official CU tool; context management with rolling buffer and server-side compaction for long-running agents.
