# /localbacklog — Add Item to Local Project Backlog

Add the text that follows the command as a new scored backlog item to the current project's backlog file.

---

## Steps

**1. Find the backlog file**

Look for a `backlog/` directory in the current working directory. If multiple files exist, pick the most relevant one based on context. If none exists, tell the user and stop.

**2. Read project context**

Read the backlog file in full. If a PRD exists (check `prd.md` or `contracts/*/` for `*-prd.md`), read it to understand the project vision — you'll use it to score value.

**3. Score the item**

Score on three dimensions:

- **Value (1–10):** How much does this advance the core product vision as defined in the PRD? Consider: operational impact for the primary user, contribution to AI/intelligence goals, revenue or retention leverage, and strategic importance. Be honest — a 10 is rare.

- **Feasibility (1–10):** How easy is this to implement given the existing tech stack (read `CLAUDE.md` if present)? 10 = pure config or a trivial query. 1 = requires a new external service, significant architecture change, or unknown unknowns.

- **Token cost:** Rough implementation effort expressed as token spend:
  - **S** — under 5k tokens (small config change, one file, one query)
  - **M** — 5–20k tokens (new server action + UI component, schema migration)
  - **L** — 20–60k tokens (new feature surface, multiple interconnected files)
  - **XL** — 60k+ tokens (new subsystem, external integration, architectural change)

**4. Write the item**

Append to the backlog file using this format:

```markdown
## [N]. [Title]

| Value | Feasibility | Token cost |
|-------|-------------|------------|
| X/10  | X/10        | S / M / L / XL |

[2–4 sentence description. What it is, why it matters, what it unlocks. Note any dependencies on other backlog items or milestones.]

**Implementation notes:** [Key files, schema changes, or external dependencies to be aware of. One short paragraph or bullet list.]

---
```

Number items sequentially based on existing items in the file.

---

## Principles

- Score honestly. Don't inflate value to make items feel important.
- Token cost is a proxy for effort — bias toward overestimating, not under.
- If the item depends on another backlog item, say so explicitly.
- Keep descriptions tight. This is a working reference, not a spec.
