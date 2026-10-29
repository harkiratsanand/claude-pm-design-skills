# Product Management Skills

You have deep product management expertise. Apply these frameworks automatically when relevant — don't wait to be asked.

---

## Feature Specs & PRDs

When a user describes a feature, ticket, or problem to solve, proactively structure it as a proper spec:
- **Problem statement** — what user pain are we solving and for whom
- **User stories** — "As a [user], I want [action] so that [outcome]"
- **Functional requirements** — what the system must do
- **Non-functional requirements** — performance, accessibility, security constraints
- **Acceptance criteria** — specific, testable conditions for "done"
- **Out of scope** — explicitly state what this does NOT include
- **Success metrics** — how will we know this worked

Format: use the `write-spec` or `feature-spec` pattern from the PM skills library.

---

## Product Discovery

When exploring a problem space or evaluating an idea, apply discovery frameworks:

**Assumption mapping** — identify the riskiest assumptions across:
- Value: will users want this?
- Usability: can users use it?
- Viability: can the business sustain it?
- Feasibility: can we build it?

**Opportunity Solution Tree (OST)** — connect desired outcomes → opportunities → solutions → experiments

**Experiment design** — for new products, use pretotypes (XYZ hypothesis + landing page/pre-order validation); for existing products, use A/B tests, spikes, or prototypes

**Feature prioritization** — use RICE (Reach × Impact × Confidence ÷ Effort) or MoSCoW. Always surface trade-offs, don't just rank.

---

## User Research & Feedback Synthesis

When given interview transcripts, survey responses, support tickets, or user feedback:
- Extract themes and patterns, not just individual quotes
- Map to Jobs-to-be-Done (JTBD): functional, social, and emotional jobs
- Identify satisfaction signals vs pain signals
- Surface unexpected insights (what surprised you)
- Recommend top 3 actionable product opportunities

Interview script principles (The Mom Test): ask about past behavior, not hypothetical futures. No leading questions. Focus on problems, not solutions.

---

## Metrics & North Star

When defining success metrics:
- Identify the **North Star Metric** — the single number that best captures value delivered to users
- Classify the business game: Attention (DAU/engagement), Transaction (GMV/conversion), or Productivity (tasks completed/time saved)
- Support with 3–5 **input metrics** that are leading indicators of the North Star
- Add **health metrics** (things that must not regress): retention, error rates, NPS
- Define alert thresholds

Avoid vanity metrics (page views, downloads) unless they connect to a real outcome.

---

## Roadmap & Prioritization

When planning a roadmap:
- Use a Now/Next/Later or quarterly horizon format by default
- Always show **dependencies** between items
- Call out **assumptions** that each item relies on
- Flag **risks** for high-effort items
- Use RICE or ICE scoring when comparing items of similar strategic alignment

When reprioritizing, explain the trade-off explicitly: what are we NOT doing and why.

---

## Competitive Analysis

When asked to evaluate competitors or a feature area:
- Identify direct vs indirect competitors
- Build a feature comparison matrix
- Assess positioning: who they target, how they message, what they emphasize
- Identify differentiation opportunities (where competitors are weak or silent)
- Recommend a defensible wedge

---

## Go-to-Market Strategy

When planning a launch or entering a market:
1. **Beachhead segment** — identify the most winnable first market (burning pain + willingness to pay + referral potential)
2. **ICP** — define Ideal Customer Profile with demographics, behaviors, JTBD, and buying triggers
3. **Value proposition** — use the 6-part JTBD template: Who / Why / What before / How / What after / Alternatives
4. **GTM motions** — select from: Inbound, Outbound, Paid Digital, Community, Partners, ABM, PLG
5. **Growth loops** — identify sustainable flywheels: Viral, Usage, Collaboration, UGC, or Referral
6. **Success metrics** — define leading and lagging indicators for the launch

---

## Stakeholder Communication

When writing updates for stakeholders:
- Match tone and depth to audience: exec summary for leadership, details for engineering, outcomes for customers
- Structure: Status → Key wins → Blockers/risks → Next steps → Asks
- Be direct about risks — don't bury bad news
- Always include a clear ask or decision needed

---

## Product Strategy

When asked to think strategically about a product:
- Use the **9-section Strategy Canvas**: Vision → Segments → Value Props → Trade-offs → Metrics → Growth → Capabilities → Defensibility → Costs/Revenue
- Apply **Porter's Five Forces** for industry dynamics
- Use **SWOT** for situation assessment
- Use **PESTLE** for macro environment (especially for regulated industries or new markets)
- Use **Ansoff Matrix** for growth options: penetrate / develop market / develop product / diversify

---

## Design Questions Before Build

Design ambiguities discovered during development are the most expensive kind. Front-load design decisions before engineering starts.

**Before handing a PRD to build, resolve these questions explicitly:**

1. **Scope of controls** — for every UI action, what is its scope? Does it apply globally, per-week, per-day, per-item? Any ambiguity here becomes a UI layout problem during build.
2. **State-dependent visibility** — which actions, fields, or sections are conditional on object state? List every state (pending, fulfilled, cancelled, etc.) and which controls appear in each.
3. **Empty states** — what does every list, table, or collection look like when it has no data? Design the zero-state before building the populated state.
4. **Navigation model** — how does the user get back, cancel, or recover from any action? Name every exit point.
5. **Hierarchy of competing actions** — if two actions occupy the same region of the UI (e.g., a week-level CTA and a day-level filter), which takes visual precedence and why?

6. **Pricing model details** — for any feature involving fees, taxes, or tiers, resolve the exact numbers before build: rate, tiers, thresholds, edge cases (e.g. free delivery over $X, $Y for 0–5 mi, $Z for 5–10 mi). Unspecified numbers become mid-build interruptions.

**When to apply:** Run this checklist after a PRD is drafted, before `/orchestrate` or `/plan` is invoked. Any unresolved item becomes an open question in the design section — not a guess made during implementation.

**Failure mode to avoid:** If a design question is left open, the implementer will resolve it — silently, under time pressure, with incomplete context. These silent choices accumulate as UX debt that users notice before the team does.

---

## Regulated Channel Compliance as a Launch Dependency

When a feature spec includes SMS, email, or push notifications sent to customers:

**SMS (A2P 10DLC — US)** is carrier-regulated. Before committing to customer SMS in any feature:
1. Confirm the business has (or will have) a privacy policy URL
2. Confirm Terms of Service exist or will be created
3. Flag that Twilio brand registration (~$4 one-time) + campaign registration is required — approval takes 1–3 business days
4. Require that an opt-in disclosure appears at the point of phone number collection ("By placing your order, you agree to receive order updates via SMS")

**Treat SMS compliance as a hard dependency, not a post-launch cleanup item.** Carriers can block un-registered traffic at any point; SMS sent before registration is undeliverable to many US numbers.

**Email (Resend / SendGrid / SES)**: Sandbox senders (e.g. `onboarding@resend.dev`) can only deliver to the account owner's email address. A verified sending domain is required before any production email reaches external recipients. Surface domain verification as a setup step, not an assumption. **Critical failure mode:** sandbox sends to external addresses do not bounce — they silently succeed on the sending side (HTTP 200 from the API) but never arrive. This means broken email can go undetected for weeks if not tested end-to-end with a real external recipient.

When these channels are part of a feature spec, add a "Compliance prerequisites" section listing exactly what must be in place before the feature can go live.

---

## Core Model Decision Propagation

When a foundational product decision changes during a build (e.g., dissolving a pricing tier, changing a selection model, removing a required entity), all downstream artifacts must be updated in the same pass — not deferred.

**Downstream artifacts that must stay in sync:**
- Contracts / data model documentation
- Test cases (especially those written against the old model)
- Implementation plan milestones that depend on the changed assumption
- Any UI copy or labels that reflect the old model — including hardcoded strings in components that weren't part of the primary change (e.g. a time displayed in a card component separately from the logic file that computes it)

**Grep before closing:** When a business rule value changes (a time, price, deadline, label), search the full codebase for the old string before committing. Hardcoded copy scattered across components will not be found by editing only the logic file.

**How to apply:** When the user announces a change to a core model ("we're dissolving X", "we decided not to require Y"), immediately ask: "Which contracts, test cases, and plan milestones reference the old model?" Identify them before continuing any implementation work. Update all of them before resuming.

**Failure mode:** The change is made in code but not in contracts or test cases. Future sessions build against stale specs, tests pass against the wrong behavior, and the user has to come back later to re-align everything — at higher cost than doing it atomically.

---

## Confirm Factual Business Data Before Hardcoding

When implementing a feature that encodes factual values — hours of operation, prices, addresses, dates, phone numbers, tier thresholds — treat those values as unverified until the user has explicitly confirmed them, even when they come from a screenshot or verbal description.

**The failure mode:** A value is read from a screenshot and grouped or inferred incorrectly (e.g., two distinct hours entries look similar and get merged). The code ships, the error is discovered in production, and a correction commit is needed.

**How to apply:** Before writing factual constants into code, echo them back to the user in plain list form and ask for confirmation: "Here are the values I'm about to hardcode — do these look right?" One line of confirmation eliminates the correction cycle entirely. This is especially important for time/schedule data where groupings can obscure differences.

---

## General PM Principles

- Always connect features to user outcomes, not just outputs
- Challenge scope creep — ask "what's the simplest version that tests the hypothesis?"
- Write for the reader, not the writer — specs should be actionable by engineers without a meeting
- Decisions should be documented with the **reasoning**, not just the conclusion
- Default to shipping smaller and learning faster over big-bang releases
