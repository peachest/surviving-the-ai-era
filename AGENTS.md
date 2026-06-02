# AGENTS.md — ai-age-defensive-programming

## Project Structure

Two canonical READMEs, always kept in sync:

| File                                          | Language | Purpose                                              |
| --------------------------------------------- | -------- | ---------------------------------------------------- |
| `README.md`                                   | English  | Techniques, Alternative Angles, Real-World Failures  |
| `README-zh.md`                                | Chinese  | Same content, translated in deadpan satire style     |
| `references/index.md`                         | English  | Curated external references with URLs + descriptions |
| `.agent/skills/add-technique-from-reference/` | —        | Skill for adding content from external URLs          |

## Cross-Reference Rules

When adding a **new technique** or **new incident**, update ALL of these locations in both `README.md` and `README-zh.md`.

### A. When Adding a Technique (N = current count + 1)

1. **Insertion point** — insert right before:
   - EN: `## Alternative Angle: By Attack Surface`
   - ZH: `## 换个角度：按攻击面`

2. **Count update** — change "The N techniques above" → "The N+1 techniques above" in the first sentence of Alternative Angle: By Attack Surface.

3. **Layer assignment** — add N to the appropriate layer in By Attack Surface:
   - **Prompt Layer** — AGENTS.md, ADRs, docs, learning, domain vocabulary
   - **Tool Layer** — hooks, config, permissions, feedback speed
   - **Context Layer** — context, memory, history
   - **Orchestration Layer** — agent architecture, workflow, sub-agents

4. **Defense objectives** — if relevant, add `→` chain to What You're Protecting:
   - **A. Make AI Code Unreviewable**
   - **B. Make AI Context Untrustworthy**
   - **C. Make the System Unsafe**
   - **D. Make the Boss Stop Believing in AI**

5. **Verify** — `grep -c "^### [0-9]" README.md README-zh.md` must return identical counts.

### B. When Adding an Incident

1. **Classify** — place in correct sub-section:
   - `### Production Incidents` — general AI failures (not coding agents)
   - `### Agent-Caused Production Destruction` — coding agent caused specific damage

2. **Format**:

   ```markdown
   - **[Title](url)**
     (YYYY-MM) — What happened. Key detail. Consequence.
   ```

3. **Insert** at end of sub-section, before any `*Know of another incident?*` line.

4. **Verify** — both READMEs must list same incidents in same order.

### C. When Adding a Reference URL

Add to `references/index.md` under the appropriate category:

```markdown
- **[Title](url)** — One-sentence description.
```

## Writing Style

See `.agent/references/writing-style.md` for the full writing style guide (technique structure, deadpan satire rules, Chinese translation conventions, anti-patterns).
