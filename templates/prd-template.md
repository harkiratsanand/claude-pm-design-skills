# PRD Structure

Use this structure, in this order, for every PRD. Sections marked **(agent-critical)** are consumed directly by `/eval`, `/plan`, `/run-tests`, and `/uat` — write them with machine-parseable precision, not stakeholder prose.

---

### 1. Overview
2–4 sentences. What is this, why does it matter, what is the core bet? Frame in terms of user goal and business goal.

---

### 2. Problem Statement
- **User problem** — what pain or unmet need are we solving? Specific about current state vs. desired state.
- **Business goal** — what does this achieve?
- **Constraint or opportunity** — what is driving this now?

Prose. No bullets.

---

### 3. Goals
What this achieves. Measurable where possible. Separate primary from secondary.

---

### 4. Non-Goals **(agent-critical)**
What this explicitly excludes. Each item must include a reason.

Format: `- [Thing] — [why it's excluded]`

A non-goal without a reason is a cut feature. A non-goal with a reason is a defensible boundary that agents can use to reject out-of-scope work without asking.

---

### 5. Core Model Decisions **(agent-critical)**
The load-bearing choices — decisions that, if wrong, require rewriting rather than refactoring. Every item must be resolved before requirements are written.

For each decision:
- **Decision:** What was chosen
- **Alternatives considered:** What else was on the table
- **Why this one:** Specific reason
- **Reversal cost:** Low / Medium / High

Any High-reversal-cost decision based on an unvalidated assumption must appear in Risks (§9).

---

### 6. User Stories
Cover all user types who interact with the system.
Format: `As a [user], I need [capability] so that [outcome].`

---

### 7. State Model **(agent-critical)**
For each primary entity in the system: every state it can be in, valid transitions, and which actions are available in each state.

```
### [Entity name]

| State | Description | Available actions |
|---|---|---|
| [state] | [what it means] | [comma-separated action names] |

Transitions:
- [state A] → [state B]: triggered by [action/event]
- [state A] → [state C]: triggered by [action/event]
```

An agent without the state model will show unavailable actions or hide available ones. This is the contract for conditional UI rendering and server-side guards.

---

### 8. Requirements **(agent-critical)**

#### Functional

Each requirement must be written in present tense ("The system [verb]..."), specific enough to assert as a test, and accompanied by acceptance criteria written as concrete scenarios.

```
**REQ-N.N** — [requirement text]
Testability: Runnable | Needs seed | Needs mock time | Non-automatable
AC:
- Given [precondition], when [action] → [exact expected output]
- Given [edge case], when [action] → [exact expected output]
```

**Testability classifications:**
- **Runnable** — no special setup needed; can execute against a clean app
- **Needs seed** — requires data to exist in the DB before the test runs; specify what
- **Needs mock time** — outcome depends on current time; requires server-side time override to be automatable
- **Non-automatable** — requires human judgment, external hardware, or an unavoidable external dependency; mark reason

If you cannot write the AC scenarios without knowing a variable that hasn't been decided yet, the requirement is not done — it is an assumption. Resolve it before proceeding.

#### Non-Functional
Performance, security, or reliability constraints that actually constrain the build. No generic best practices.

---

### 9. Risks
Focus on reversal cost and unvalidated assumptions. Any Core Model Decision with High reversal cost tied to an unvalidated assumption must appear here.

| Risk | Type | Description | Severity | Mitigation |
|---|---|---|---|---|

Risk types: Value, Usability, Feasibility, Business Viability
Severity: High / Medium / Low

---

### 10. Open Questions
Flat list. Unresolved decisions that will block build or require a PRD update when answered.

Format: `- [Question] — [context, who can answer, whether it blocks a specific requirement]`

---

### 11. Out of Scope
Explicitly deferred items, why, and whether/when they might be revisited.
