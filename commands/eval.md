# /eval — Eval-First Domain Specification

Write domain behavior as executable assertions **before** writing a PRD, contracts, or implementation. Use the difficulty of writing expected outputs as a signal — if you can't write them unambiguously, the spec isn't done.

This command replaces the traditional "write PRD → build → test" flow with "write assertions → validate assumptions → write PRD → build."

---

## When to use this

Use `/eval` at the **start** of a new project or feature, before any contracts or implementation. The output feeds directly into `/prd` (or `/prd-lite`) and then `/plan`.

Do not use this to retrofit tests onto finished code. Use `/run-tests` for that.

---

## Step 0 — Identify the domain

Ask the user: **What behavior are we specifying?**

Narrow to the smallest slice that makes sense to assert independently:
- A pricing calculation
- A business rule (eligibility, cutoff, state transition)
- An ordering flow (steps, validation, confirmation)
- A permission or auth boundary

One domain per run. If the user describes multiple domains, list them and ask which to start with.

---

## Step 1 — Extract the load-bearing decisions

Before writing a single assertion, surface every decision that, if wrong, would invalidate the assertions themselves.

For each decision:
1. State the decision explicitly
2. State the current assumption (what we're assuming is true)
3. Rate the reversal cost if the assumption is wrong: **Low / Medium / High**

Use this format:

```
Decision: [what the system must decide]
Assumption: [what we're currently assuming]
Reversal cost: Low / Medium / High
Reason: [why reversing it is easy or expensive]
```

**Do not proceed to Step 2 if any High-cost decision is unresolved.** Surface it to the user and resolve it first.

---

## Step 2 — Write domain assertions

For each load-bearing decision or behavioral rule, write 3–5 concrete scenarios with **exact, unambiguous expected outputs**.

Format:
```
Scenario A: [exact inputs] → [exact expected output]
Scenario B: [exact inputs] → [exact expected output]
Scenario C: [edge case] → [exact expected output]
```

**Rules for assertions:**
- Expected outputs must be specific enough to assert in a test — no ranges, no "approximately", no "should"
- Include at least one boundary condition (just inside the rule) and one just-outside case
- Include the zero/empty case if the domain has one
- If the output is a UI state, name it exactly as it would appear ("Window closed" not "error state")
- If the output involves a computed value, show the arithmetic

**Stop and resolve before proceeding if:**
- You cannot write the expected output without a decision that hasn't been made yet
- The expected output changes depending on a variable you assumed was unrelated
- Writing the scenarios reveals more conditional branches than the design anticipated

Difficulty writing scenarios is a signal that the spec is not done. Do not push through — apply the Complexity Challenge (is there a simpler model?), resolve, and re-run.

---

## Step 3 — Classify testability

For each assertion, assign a testability class:

| Class | Meaning |
|-------|---------|
| **Runnable** | Can be asserted end-to-end against a dev server with no special setup |
| **Needs seed** | Requires specific data to exist before the assertion can be checked |
| **Needs mock time** | Behavior depends on wall-clock time; requires server-side time injection |
| **Needs auth** | Requires a specific user session or role |
| **Non-automatable** | Requires human judgment, physical delivery, or real external services |

Any "Needs mock time" class must note whether a server-side time override mechanism will need to be built. Flag this as an **infra requirement** that must be included in the build plan.

---

## Step 4 — Output the domain eval file

Write `domain-evals/[domain-name].md` in the project root using this structure:

```markdown
# Domain Eval: [Domain Name]

**Date:** [today]
**Status:** Draft / Validated / Superseded

---

## Load-Bearing Decisions

| Decision | Assumption | Reversal Cost | Reason |
|----------|------------|---------------|--------|
| [decision] | [assumption] | Low/Med/High | [reason] |

---

## Assertions

### [Rule or behavior name]

```
Scenario A: [inputs] → [expected output]
Scenario B: [inputs] → [expected output]
Scenario C: [edge case] → [expected output]
```

**Testability:** Runnable / Needs seed / Needs mock time / Needs auth / Non-automatable
**Infra requirement:** [if applicable — what must be built to automate this]

---

[Repeat for each rule]

---

## Open Questions

[Any decision from Step 1 that could not be resolved — must be resolved before PRD is finalized]

---

## Infra Requirements

[List any test infrastructure that must be planned before build starts — e.g., server-side time injection, seed scripts, auth setup]
```

---

## Step 5 — Gate check

Before handing off to `/prd` or `/plan`:

1. Are all High-cost decisions resolved? If not, stop.
2. Can every assertion be written with exact expected outputs? If not, stop.
3. Are all infra requirements captured in the eval file? If not, add them.
4. Does the assertion set cover the boundary conditions (just inside, just outside)? If not, add them.

Only proceed to `/prd` when the gate check passes. The domain eval file is the authoritative spec for these behaviors — the PRD describes intent, the eval file describes correctness.

---

## Principles

- **Difficulty is a signal.** If you can't write the expected output, the decision isn't resolved — it's assumed.
- **Assertions before prose.** A spec written as assertions is harder to misread than a spec written as requirements.
- **One assertion file per domain.** Keep them focused. A file that asserts pricing logic should not also assert auth rules.
- **The eval file lives with the code.** Check it into the repo. It is the executable version of the spec.
- **Supersede, don't delete.** When a behavior changes, mark the old assertion `Status: Superseded` and write new assertions. This preserves a record of intentional behavioral changes.
