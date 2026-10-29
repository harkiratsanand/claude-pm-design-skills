# Requirements Discipline Skill

This skill governs how to conduct requirement gathering and PRD critique. It is active throughout any PRD session and applies both to the drafter and the critic voice.

---

## Load-Bearing Decisions Interview (mandatory before drafting)

Before writing any PRD section, run this interview batch. These are the decisions that, if wrong, would require rewriting the implementation rather than refactoring it. Do not proceed to drafting until every item has a stated, explicit answer.

**Third-party integration config**
- If the feature integrates with an external system that uses org-specific identifiers (Xero account codes, QuickBooks chart of accounts, Stripe product IDs, etc.), obtain that config before writing any code.
- Default/template values (e.g. Xero's UK default account `200`) are invalid in custom orgs. Hardcoding them will produce silent failures in production.
- Required: ask for the account codes, product IDs, or category mappings during requirements gathering — not during debugging.

**Pricing / monetization model**
- What is the exact mechanism? Fixed tiers, per-item, hybrid, subscription?
- Are there surcharges, variants, or conditional pricing?
- What is the simplest pricing model that still works for this product?

**Core data model**
- What are the primary entities and their key relationships?
- Can the same item appear multiple times (quantity), or is each selection unique?
- What fields are required vs. optional vs. derived?

**UX flow structure**
- How many steps does the user expect?
- What is the minimum number of steps that still delivers the core value?
- What must be shown together vs. can be shown sequentially?

**Default vs. explicit states**
- What is included by default that users can opt out of?
- What requires explicit selection before it is included?

**Simplest viable version**
- If you stripped this to the bare minimum that still solves the problem, what would it look like?
- What is deliberately being added on top of that, and why?

If the user is unsure on any item, surface the options and tradeoffs explicitly. Do not pick silently. A decision made here without validation is a risk — flag it in the PRD's Risk section with High reversal cost.

---

## Complexity Challenge (active throughout critique)

For every non-trivial design decision in the PRD, ask:
- Is there a simpler version of this that achieves the same goal?
- If yes, is there a concrete reason to prefer the more complex version?
- If no concrete reason exists, recommend the simpler version.

Complexity that isn't justified by a specific user need or technical constraint is a liability. The right amount of complexity is the minimum needed to solve the stated problem.

---

## Load-Bearing Decision Audit (before finalizing the PRD)

Before the PRD is considered ready for orchestration:
1. List every Core Model Decision from Section 6.
2. For each one: is it resolved with a stated reason, or is it an assumption?
3. For each assumption: what is the reversal cost if it turns out to be wrong?
4. Any High reversal-cost assumption must be flagged as a risk in Section 16.
5. If any load-bearing decision remains unresolved, the PRD is not ready. Do not hand it to `/orchestrate`.

---

## Load-Bearing Assumption Stress Test (blocking — runs before contracts)

The load-bearing decision interview identifies risky assumptions. This step forces resolution before any assumption-dependent logic enters contracts.

For each load-bearing decision identified in the interview, produce 3–5 concrete scenarios with exact, unambiguous expected outputs:

```
Scenario A: [inputs] → [expected output]
Scenario B: [inputs] → [expected output]
Scenario C: [edge case inputs] → [expected output]
```

**The scenarios may be anything the project calls for** — transaction totals, permission outcomes, state transitions, validation results, computed values. The form doesn't matter. The requirement is that expected outputs are specific enough to assert in a test.

**Stop and resolve before proceeding if:**
- You cannot write the expected output without knowing a variable that hasn't been decided yet
- The expected output changes depending on a factor that was supposed to be unrelated
- Writing the scenarios reveals more conditional branches than the design anticipated

Difficulty writing scenarios is a signal, not a blocker to push through. It means the decision isn't actually resolved — it's assumed. Apply the Complexity Challenge, find the simpler model, and re-run. Flag the decision as High reversal cost in the PRD's Risk section.

---

## Blocking Rule

A PRD with unresolved load-bearing decisions is not a finished PRD. It is a list of assumptions dressed as requirements. Do not let it proceed to orchestration.

---

## Non-Goals Must Include a "Why"

When writing or critiquing a Non-Goals section, each item must include a reason — not just a name.

Bad: `- Multi-currency support`
Good: `- Multi-currency support — all customers pay in a single currency; adding currency handling would complicate pricing logic with no current benefit`

**Why this matters:** A non-goal without a reason is just a list of cut features. A non-goal with a reason is a defensible decision. The reason is what allows someone to correctly evaluate whether a new request falls inside or outside scope. Without it, every non-goal is re-litigated.

When reviewing a PRD, flag any non-goal that lacks a reason as incomplete. Do not mark the PRD ready until each non-goal has a stated rationale.

---

## Implicit Linking as Named Technical Debt

When two data entities are implicitly linked by matching on a human-entered field (e.g., customer name, phone number, email) rather than a foreign key, treat this as technical debt — not an implementation detail.

This pattern must be:
1. **Named explicitly** in the PRD's Risks section: "Customer matching relies on [field] — not a foreign key. Duplicates and typos will produce orphaned records."
2. **Assigned a reversal cost**: migrating implicit links to FK relationships after data exists is expensive (requires deduplication, backfills, schema changes).
3. **Surfaced as a load-bearing decision**: the choice between implicit linking and relational linking is not an implementation detail — it determines data integrity at scale.

Do not treat fuzzy-match or name-based linking as a temporary convenience that can be fixed later without cost. It should appear in the PRD before the first line of code is written.

---

## Pricing Constants Must Have a Single Source of Truth

When a pricing value (price tier, surcharge, discount threshold) appears in more than one code path, it must live in exactly one place.

**The failure mode:** A `pricing.ts` module defines canonical prices used by the admin backend, running totals, and invoices. A UI component separately hardcodes the same values in a display array. A price change updates `pricing.ts` but not the component — customers see wrong prices until someone notices.

**Rule:** Any price that appears in both a write path (order submission, invoice, admin backend) and a read path (customer form, product listing) must be sourced from a single constant. Hardcoded arrays in UI components that mirror a pricing module are a latent bug.

**How to identify:** When writing a display component that shows prices, grep for the same numbers in other files. If they appear elsewhere as a separate definition, extract to a shared constant and import in both places.

---

## Shared Derived Values Must Have a Single Source of Truth

When the same value is independently derived in two or more code paths (e.g., a write path and a read path), treat the formatting logic as a load-bearing decision — not an implementation detail.

**Rule:** If two paths derive the same value separately, one of them will drift. Enforce a shared utility function that both call, with a test asserting they produce identical output.

**How to identify this pattern:**
- A value written in one place (e.g., admin-side record creation) is later queried by another place (e.g., the customer-facing page)
- Both paths call the same underlying function but format or serialize its output differently
- Tests for each path pass in isolation but the system breaks when run on a machine with a different timezone, locale, or environment

**Required actions when the pattern is found:**
1. Extract the formatting into a named shared utility (e.g., `formatWeekOf(date: Date): string`)
2. Replace all inline derivations with calls to the shared utility
3. Add a test that asserts the shared utility produces the same output as the previous inline convention — this test will catch future divergence

**Why this matters:** Timezone-dependent formatting bugs (e.g., `date-fns format()` vs `.toISOString()`) are invisible when the dev machine and CI run in the same timezone. They surface only in production or on machines in a different timezone — the worst time to discover them. A shared utility with a test eliminates the entire class of divergence.

---

## Data Fetch Filter Completeness

When specifying a data fetch requirement (query, API call, Supabase select, etc.), enumerate **all** filter dimensions — not just the primary entity type.

**Common dimensions that get omitted:**
- Publication/availability state (e.g., `available = true`, `status = 'published'`)
- Date or week scope (e.g., `week_of = currentWeek`)
- Soft-delete / cancellation exclusion (e.g., `order_status != 'cancelled'`)
- Ownership or tenant scope (e.g., `user_id = currentUser`)

**The failure mode:** A query is specified as "fetch products for this week" but the filter only checks `week_of`. Without `available = true`, unpublished products appear on the customer-facing page. The bug passes code review because the spec said "for this week" and that filter is present — the missing dimension was never written down.

**Rule:** Every data fetch spec must explicitly state which records are included AND which are excluded. "Fetch products" is not a complete spec. "Fetch products where `available = true` AND `week_of = currentWeek`" is.

---

## UAT and Live-Access Flows

When writing UAT test plans or acceptance criteria for features that involve real-time data, auth-gated pages, or multi-step workflows:

- **Mark which tests require live access** — distinguish tests that can be validated against static data from tests that require a running instance with real (or seeded) data.
- **Auth-gated pages must include login steps** in the test procedure — do not assume the tester is already authenticated.
- **Real-time features** (subscriptions, live counters, status updates) require two concurrent sessions or a known event trigger — specify this in the acceptance criteria.
- **Multi-step flows** (e.g., order → fulfill → payment) must be tested end-to-end, not step-by-step in isolation — specify the full sequence as one test case.

A UAT test that assumes ambient context (already logged in, data already present, other tab already open) will produce false passes in clean environments.
