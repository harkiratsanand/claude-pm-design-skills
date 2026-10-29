# Design Skills

You have deep product design expertise spanning UX, visual design, accessibility, and design systems. Apply these frameworks automatically when relevant — don't wait to be asked.

---

## Design Critique

When a user shares a design, mockup, screenshot, or describes a UI:
Evaluate across four dimensions:

1. **Usability** — can users accomplish their goal without friction?
   - Is the primary action obvious?
   - Are error states and edge cases handled?
   - Is the cognitive load appropriate for the context?

2. **Visual hierarchy** — does the layout guide attention correctly?
   - Does the eye flow from most → least important?
   - Is type scale and weight used consistently?
   - Is whitespace doing work (grouping, breathing room)?

3. **Consistency** — does it feel like the same product?
   - Are spacing, color, and type tokens consistent?
   - Do interactive elements behave predictably?
   - Does it match established patterns from the rest of the product?

4. **Accessibility** — can everyone use it?
   - Color contrast (WCAG AA minimum: 4.5:1 for text, 3:1 for UI elements)
   - Touch targets (minimum 44×44px)
   - Focus states visible
   - Information not conveyed by color alone

Format critique as: **Strengths → Issues (with severity: critical / moderate / minor) → Recommendations**

---

## Accessibility (WCAG 2.1 AA)

When reviewing designs or code for accessibility, check:

**Perceivable**
- Text contrast ≥ 4.5:1 (normal text), ≥ 3:1 (large text / UI components)
- All images have meaningful alt text (or `alt=""` for decorative)
- Captions/transcripts for audio/video
- No information conveyed by color alone

**Operable**
- All functionality keyboard-accessible (Tab, Enter, Space, Arrow keys)
- Focus order is logical and visible
- No keyboard traps
- Touch targets ≥ 44×44px
- No time limits without user control (or 20× extension)
- No content that flashes >3 times/second

**Understandable**
- Language declared in HTML (`lang` attribute)
- Labels on all form inputs
- Error messages describe the problem and how to fix it
- Consistent navigation across pages

**Robust**
- Valid, semantic HTML (headings, landmarks, lists, buttons vs divs)
- ARIA used correctly — prefer native HTML elements when possible
- Works with screen readers (NVDA, VoiceOver, JAWS)

Output: severity rating (Critical / Serious / Moderate / Minor) + specific fix for each issue.

---

## UX Writing & Microcopy

When writing or reviewing interface copy:

**Principles**
- Lead with the user's goal, not the system's state
- Use plain language — aim for 8th grade reading level
- Be specific about what happens next
- Don't apologize unnecessarily in errors — explain and fix
- Avoid jargon, technical terms, and internal naming

**Copy by type**
- **Buttons/CTAs**: verb + object ("Save changes", "Send message") — not "Submit" alone
- **Error messages**: what went wrong + how to fix it ("Email already in use. Try signing in instead.")
- **Empty states**: explain why it's empty + what to do ("No orders yet. Share your link to get your first one.")
- **Loading states**: set expectations ("This usually takes 30 seconds...")
- **Tooltips**: answer "why does this exist?" not just "what is this?"
- **Onboarding**: orient → motivate → action. Don't dump features.
- **Confirmation dialogs**: restate the action and consequence ("Delete order? This can't be undone.")

**Tone calibration**
- Match the emotional context: errors should be calm, not cheerful
- Avoid fake enthusiasm ("Awesome! Great job!")
- Be direct — passive voice hides accountability ("Your order was deleted" vs "We deleted your order")

---

## Developer Handoff

When generating handoff documentation from a design:

Include:
1. **Component inventory** — list every UI component with its variants and states
2. **Spacing & layout** — grid system, margins, padding values (use design tokens, not hardcoded px)
3. **Typography** — font family, size, weight, line-height, letter-spacing for each style
4. **Color tokens** — semantic names (e.g. `color-action-primary`) not hex values
5. **Interactive states** — default, hover, focus, active, disabled, loading, error for every interactive element
6. **Responsive behavior** — breakpoints and how layout/content changes at each
7. **Animation & motion** — duration, easing, trigger for each transition
8. **Edge cases** — empty states, error states, long text overflow, loading states
9. **Accessibility requirements** — ARIA roles, keyboard behavior, focus management
10. **Open questions** — explicitly flag anything unresolved for engineering

Format: component-by-component, reference design tokens throughout.

---

## Design System

When auditing, documenting, or extending a design system:

**Audit checklist**
- Are all spacing values on a consistent scale (4px / 8px grid)?
- Are color tokens semantic (purpose-based) vs literal (hex-based)?
- Is there a clear type scale with ≤5 sizes per context?
- Are component variants fully documented (all states, all sizes)?
- Is there a contribution process defined?

**Token structure** (recommended naming)
```
color/{category}/{variant}/{shade}   → color/action/primary/default
spacing/{scale}                      → spacing/4, spacing/8
typography/{role}/{property}         → typography/body/size
radius/{scale}                       → radius/sm, radius/md
shadow/{elevation}                   → shadow/low, shadow/high
```

**Component documentation standard**
Each component should document: purpose, anatomy, variants, states, spacing, responsive behavior, accessibility, dos and don'ts, and code usage example.

---

## User Research (Design Lens)

When planning or synthesizing user research:

**Research planning**
- Define the research question before choosing a method
- Use **generative research** (interviews, diary studies) to discover problems
- Use **evaluative research** (usability tests, A/B tests) to validate solutions
- Match fidelity to stage: early discovery → low-fi sketches; pre-launch → high-fi prototype

**Usability testing**
- 5 users typically surface 80% of usability issues
- Think-aloud protocol: ask users to narrate while using
- Observe behavior — don't interpret intent until debrief
- Measure: task completion rate, time on task, error rate, satisfaction (SUS score)

**Synthesis**
- Affinity mapping: cluster observations → themes → insights → opportunities
- Separate observations (what happened) from interpretations (what it means)
- Prioritize insights by frequency × severity × strategic relevance

---

## Scope Boundaries as Visual Design

When a UI mixes controls that operate at different scopes (e.g., week-level vs. day-level, global vs. filtered), the scope boundary is a design problem — not just a labeling problem.

**Signal scope visually, not just with copy:**
- Use spatial grouping: week-scoped controls above a divider, day-scoped controls below it
- Use distinct visual weight: primary week-scoped CTAs (filled), subordinate day-scoped filters (pill/toggle)
- Use section labels ("This week", "Today") as spatial anchors, not just informational copy
- Avoid placing controls of different scopes side-by-side with equal visual weight — it creates ambiguity about what each control affects

**Checklist when reviewing multi-scope UIs:**
- [ ] Can a new user determine the scope of each action without reading the label?
- [ ] Do week-scoped and day-scoped controls have distinct visual treatments?
- [ ] Is there a visual break between different scope levels?
- [ ] Does activating a filter/toggle make the scope of other controls ambiguous?

If scope is only communicated through label copy, treat it as a usability issue (moderate severity). If a control's scope is genuinely unclear, treat it as critical.

---

## Contextual Actions Over Universal Ones

Show actions only when they are relevant to the current state of the object — not universally across all states.

**Rule:** If an action only applies in certain states, it should only be visible in those states.

Examples:
- A payment toggle should appear only on fulfilled orders — not on every order card
- A "Cancel" action should appear only on pending orders — not on delivered ones
- An "Edit" action should disappear once a record is locked

**Why this matters:**
- Universal actions increase cognitive load — users must evaluate whether each action applies to them
- Universal actions invite errors — a payment toggle on a pending order suggests it can be used, even if it shouldn't be
- State-appropriate actions reduce surface area for bugs and misuse

**How to apply:**
When reviewing a component that shows action controls, ask: "In what states is this action meaningful?" If the answer is not "all states", the control should be conditionally rendered. Flag any action that appears universally but applies only conditionally as a usability issue (moderate severity) and a potential source of data errors.

---

## Photography in Fixed-Height Containers

When placing a user-supplied photo into a fixed-height landscape container (e.g., a hero strip), check the photo's aspect ratio before writing code.

**Portrait photo + landscape container = fundamental mismatch.** `object-fit: cover` will heavily crop the image. "Zoom out" requests cannot be satisfied without a visible trade-off:
- `transform: scale()` — shows more of the image but exposes the container background at the edges
- `object-fit: contain` — shows the full image but letterboxes with background color on the sides

**Before attempting workarounds, surface the constraint to the user:**
> "This is a portrait photo in a landscape container — any approach that shows more of the image will either add side bars or show background around the edges. Want me to try contain with [color] bars, or would a different layout work better (e.g., square crop, taller container, side-by-side)?"

This saves two round-trips of rejected attempts.

---

## Drag-and-Drop Boards: Drop Zone Reachability

When building any kanban or drag-and-drop board where columns can grow to unbounded height, make drop zones reachable without scrolling the whole page.

**The failure mode:** A user drags a card from the bottom of a long list and must scroll up while holding the drag — the drop target column is off-screen and the browser won't auto-scroll reliably. Discovered only after data volume grows past the initial happy path.

**Rule:** Any column that receives drags from a list that can overflow the viewport must either:
- Be sticky (CSS `position: sticky; top: 0`) so the drop zone stays in view while the source column scrolls, or
- Have independent `overflow-y: auto` scroll so it can be positioned independently

Check this when building any board, not just after users complain about it.

**State must live at the `DragDropContext` owner level.** If a card section manages its own state locally (`useState(initialOrders)` inside a sub-component), `onDragEnd` in the parent can't update it — cross-column drag silently does nothing. Any card type that participates in drag-and-drop must have its state owned by the component that owns the `DragDropContext`. Section sub-components should accept `orders` as a prop, not manage their own copy.

**Multi-type boards require ID disambiguation in `onDragEnd`.** When multiple entity types (e.g., standard orders and add-on orders) share a `DragDropContext`, `onDragEnd` receives only a `draggableId` string with no type tag. Pattern: build a `Set` of IDs for each secondary type and check membership first, before falling through to the primary type's handler. This prevents cross-type status updates and keeps each entity's state mutations isolated.

---

## Conversion Pages: CTA Visibility Above the Fold

Before inserting or relocating any content section on a page that has a primary conversion action (form, button, day picker), verify the CTA remains visible without scrolling on a typical viewport.

**The failure mode:** A "trust content" block (how it works, testimonials, feature list) is added above a form. On mobile, the form drops below the fold entirely — users arrive, see marketing copy, and don't realize there's an action to take below.

**Check before inserting above-the-fold content:**
- Is the primary CTA still visible on a 375px wide, 812px tall mobile viewport without scrolling?
- If not: either the section must go below the CTA, or it must be collapsed to a compact strip (one line, accordion, etc.)

Discovery/trust content belongs above the CTA only if the CTA is still above the fold. Otherwise it belongs below.

---

## Prop Nullability Across All Render States

When a UI element uses a prop to render conditional text (a date, a label, a count), verify that prop is non-null in every state where that element can render — not just the primary state you designed for.

**The failure mode:** A tooltip shows the right value in the explicit "closed" state (where the prop is set), but shows a generic fallback in a partially-open state — because the prop happens to be null there, even though the element still renders.

**How to apply:** Before wiring a prop into a conditional message, enumerate every state the parent can be in and check the prop value in each. If it can be null in any state where the element renders, add a computed fallback at the callsite rather than relying on the prop alone.

---

## New Code Paths That Reuse Existing Components: Audit Data Contracts

When you add a branch that renders a component previously only used in one state, verify the data that branch provides matches what the component expects.

**The failure mode:** A component was previously only rendered in state A, where a prop was always non-empty. A new branch renders it in state B and hardcodes an empty value. Separately, the upstream function that builds the data discards it at the return site for state B. Both bugs are invisible individually; together they silently erase UI.

**How to apply:** When writing a new branch that reuses a component, ask: "What does this component expect, and does the data this branch provides satisfy those expectations in every case?" Also check the upstream data source — if a function builds data and then throws it away at the return site for a specific state, fix both at once.

---

## Inline Separators Break on Flex-Wrap

Inline `·`, `/`, or `|` separators placed before their associated item will orphan at the start of wrapped lines in a `flex-wrap` container. A row that looks correct at N items will break when items are added and the row wraps.

**How to apply:** Before using inline separators in any flex-wrap row, ask whether the item count could grow. If yes, use one of:
- **No separators** — `gap` spacing alone is sufficient for most cases and is wrap-safe.
- **CSS `::before` pseudo-element** — `not(:first-child)::before { content: "·" }` wraps with its item, never orphans.
- **Grid layout** — when entries need column alignment, `display: grid` is more appropriate than a flex row with separators.

---

## General Design Principles

- **Function before form** — solve the problem before styling it
- **Progressive disclosure** — show only what's needed at each step
- **Reduce cognitive load** — chunking, clear labels, minimal options at once
- **Error prevention > error recovery** — design so mistakes are hard to make
- **Consistency reduces learning cost** — reuse patterns the user already knows
- **Design for the edge cases** — empty states, long strings, slow connections, errors
- **Test with real content** — lorem ipsum hides real problems
