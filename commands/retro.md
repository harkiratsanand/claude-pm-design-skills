# /retro — Session Retrospective

You are closing out a build, design, or planning session. Your job is to extract learnings from this session and propagate them to the right upstream files so future sessions start smarter.

You do not summarize what was built. You extract what was learned about *how to build*.

---

## Step 1 — Extract learnings

Review the current session for:

- **Design decisions that were revised** — what was the initial assumption, what changed, and why?
- **Bugs or rework traced to a process gap** — missing requirement, ambiguous non-goal, scope not resolved before build
- **Workarounds that signal a skill gap** — cases where a command or skill gave incomplete guidance and you had to improvise
- **Corrections from the user** — explicit feedback on approach, tone, output format, or behavior
- **Patterns that appeared more than once** — anything that came up repeatedly is a candidate for codification

For each candidate, write one line (do not skip to writing — route first in Step 1b):
> **[Learning]:** [What happened] → [What should change]

If nothing meaningful surfaced, say so explicitly. Do not manufacture learnings.

---

## Step 1b — Route: global vs. project-specific

For each learning identified above, decide where it belongs *before* writing it anywhere:

**Global skill/command files** (`~/.claude/skills/`, `~/.claude/commands/`) — for learnings that would improve work on *any* future project. The test: "If I opened a completely different project tomorrow, would this rule help?" If yes, it's global. **Global entries must be written product-agnostically** — no project names, no project-specific values, no example data from the current codebase. Extract the principle only.

**Project memory** (`~/.claude/projects/[project]/memory/`) — for learnings tied to this project's specific data model, business rules, naming conventions, or user preferences. These belong in memory, not in global skills.

When a learning is partly general and partly specific, extract the general principle into the global file and the specific detail into project memory.

---

## Step 2 — Map to upstream files

For each learning, identify the right destination:

| Learning type | Destination |
|---|---|
| Requirements gap (missing non-goal, implicit link, untestable acceptance criteria) | `~/.claude/skills/requirements-discipline.md` |
| Design gap (scope ambiguity, universal vs contextual action, visual hierarchy) | `~/.claude/skills/design.md` |
| PM process gap (design questions not front-loaded, roadmap sequencing) | `~/.claude/skills/product-management.md` |
| PRD drafting gap | `~/.claude/commands/prd-lite.md` or `~/.claude/commands/prd.md` |
| Build sequencing gap | `~/.claude/commands/plan.md` |
| Agent/UAT gap | `~/.claude/commands/orchestrate.md` |
| No clear home | Flag it — ask the user if a new skill file is warranted |

---

## Step 3 — Make the edits

**For global skill/command learnings:**
1. Read the target file
2. Find the most relevant section
3. Add the learning as a targeted addition — not a new section unless it truly doesn't fit anywhere
4. Keep additions concise: the principle, why it matters, how to apply it

**For project-memory learnings:**
1. Check `~/.claude/projects/[project]/memory/MEMORY.md` for an existing entry that covers this topic
2. If updating an existing memory: read the file and edit it in place
3. If creating a new memory: write a new file with this frontmatter, then add a one-line pointer to `MEMORY.md`:
```markdown
---
name: [short name]
description: [one-line description used to judge relevance in future sessions]
type: feedback | project | user | reference
---

[memory content — for feedback/project types: rule/fact, then **Why:** and **How to apply:** lines]
```

Do not rewrite existing content. Do not add learnings that are already covered. Do not pad.

---

## Step 4 — Report

After all edits, output a summary:

```
## Retro complete

Learnings captured: [N]
- [Learning 1] → [file updated]
- [Learning 2] → [file updated]

Learnings flagged (no clear home):
- [Learning] → [proposed destination or prompt to create new file]

Nothing captured:
- [Any learning that was considered but already covered or too minor]
```

---

## Principles

- A learning that isn't written down is lost the next time the same mistake happens.
- Prefer adding to existing sections over creating new ones — new sections are only warranted when a genuinely new topic emerges.
- If a pattern has appeared in two or more sessions, it must be captured — recurrence is the signal.
- The retro is not a summary of the work. It is an update to the system that does the work.
