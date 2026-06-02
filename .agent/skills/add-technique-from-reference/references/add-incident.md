# Add a Real-World AI Failure Incident

Follow this workflow when the reference documents a **specific, named production disaster** — an AI agent or LLM caused actual damage to a real organization (deleted databases, leaked secrets, production outages).

## Workflow

1. **Classify** — Production Incidents (general AI) vs Agent-Caused Production Destruction (coding agent).
2. **Format** entry in EN + ZH (2-3 factual sentences, no satire).
3. **Insert** at end of correct sub-section in both READMEs.
4. **Verify** both files have same incidents in same order.

Use `ask_user_question` if classification is ambiguous.

## Workflow: Todo-Driven

Before writing, create a sub-checklist with `todo`:

```text
1. Classify incident type (Production Incident vs Agent-Caused Production Destruction)
2. Draft English entry
3. Draft Chinese entry
4. Insert into README.md (correct sub-section, at end before closing line)
5. Insert into README-zh.md (same sub-section, same position)
6. Verify: both files match in order and content
```

Mark each `in_progress` → `completed` as you go.

## Step 1: Classify the Incident

The `## Real-World AI Failures` section has two sub-sections:

### `### Production Incidents`

General AI/LLM failures. **NOT** specifically about coding agents. Examples:
- Chatbot hallucinations that caused legal liability
- Employees leaking secrets to ChatGPT
- Research findings about AI misbehavior

### `### Agent-Caused Production Destruction`

Specific incidents where an AI **coding agent** (Claude Code, Cursor, Copilot, Replit AI, etc.) caused production damage through its own autonomous actions. Examples:
- Agent deleted a production database
- Agent force-pushed over main branch
- Agent executed destructive shell commands on a live server

Place the incident in the correct sub-section.

### Ask When Classification Is Unclear

If the incident involves both a coding agent and a broader AI system, or you're unsure which sub-section fits better, use `ask_user_question`:

```
question: "This incident involves [describe]. Which sub-section does it belong in?"
header: "Classify"
options:
  - "Production Incidents — broader AI/LLM failure, not coding-agent-specific"
  - "Agent-Caused Production Destruction — coding agent caused the damage"
  - "Both perspectives apply — let me describe and you decide"
```

## Step 2: Format the Entry

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

## Style Rules

- **Title** is the link text — punchy, factual, names the agent/tool involved. Must fit on one line in the rendered README.
- **Date** in `(YYYY-MM)` format — if only year is known, use `(YYYY)`.
- **Body**: 2–3 sentences total.
  - Sentence 1: What happened (who, what tool, what action).
  - Sentence 2: Why/how it went wrong.
  - Sentence 3: Consequence or follow-up (recovery time, policy change, public apology).
- **No satire in the incident body** — these are factual records, not jokes. The humor comes from the accumulated horror of reading them all in sequence.
- Only the epigraph at the top of the whole section (`> *"It was just a demo..."*`) can be satirical.

### When Unsure About Entry Quality

If the entry feels too vague, too long, or missing key details, use `ask_user_question`:

```
question: "Current draft: [show entry]. Does this capture the incident well?"
header: "Draft check"
options:
  - "Good — insert it"
  - "Too vague — add more specific damage detail"
  - "Too long — condense to 2-3 punchy sentences"
```

## Step 3: Insert Into Both READMEs

**Insertion point:** At the end of the appropriate sub-section, before any closing line like `*Know of another incident?*`.

Use `read` to find the exact insertion point in each file, then use `edit` to add the entry.

## Step 4: Verify

After adding to both READMEs, confirm:
- Both files list the same incidents in the same order under each sub-section
- Dates are consistent between languages
- Links are identical
- The entry appears in the correct sub-section (Production Incidents vs Agent-Caused Production Destruction)
