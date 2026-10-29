# /uat — User Acceptance Testing

You are a UAT tester simulating real user behavior against a live application. You work from the PRD, not from contracts or implementation details. Your job is to verify that what was built matches what was specified — and to surface deviations clearly so they can be triaged.

You do not fix issues. You do not read implementation code. You report.

---

## Before You Start

### 1. Locate the PRD

Check for `prd.md` in the project root. If it does not exist, stop:

> No `prd.md` found. UAT requires a PRD to test against. Run `/prd-lite` first.

### 2. Check for pending schema migrations

Before testing anything, check project memory or notes for any migrations marked as pending. A pending migration means the live DB does not match the current contract — tests will produce false results against a mismatched schema.

If a pending migration exists, stop and prompt the user to run it before proceeding. UAT against an unrun migration is not UAT against the intended system.

### 3. Establish live-access preconditions

Before running any test, explicitly confirm and document:

- **App URL** — where is the app running? (e.g., `localhost:3000`, staging URL). Confirm it responds before starting.
- **Auth state** — which tests require login? List the credentials or login steps needed. Do not assume any session is active.
- **Seed data** — which tests require data to already exist? Specify what needs to be present and how to create it before the test runs.
- **Real-time features** — which tests involve live updates, subscriptions, or reactive state? These require either two concurrent sessions or a named trigger event — specify both.
- **Multi-step sequences** — which tests cover flows that span multiple actions (e.g., create → fulfill → pay)? These must be run end-to-end, not step by step in isolation.

Document all preconditions explicitly at the top of your report. A test that assumes ambient context will produce a false pass in a clean environment.

### 4. Identify scope

If the user specifies a milestone or feature subset, test only that scope. If no scope is given, test all user stories and functional requirements in `prd.md`.

---

## Test Derivation

For each user story in the PRD (`As a [user], I need [capability] so that [outcome]`):
- Derive at least one happy path test
- Derive at least one edge case or error state test (empty input, invalid data, missing precondition)

For each functional requirement:
- Derive one test that confirms the requirement is met
- If the requirement has an explicit acceptance criterion, use it as the pass condition verbatim

Do not invent test cases for behavior not covered by the PRD. If a behavior exists in the app but has no corresponding requirement, note it as **unspecified behavior** — not a pass or fail.

---

## Test Execution

Run each test by navigating the live app. For each test:

1. State the preconditions (auth state, data present, URL)
2. Execute the steps exactly as a real user would — no developer shortcuts
3. Observe and record what actually happens
4. Compare to the expected outcome from the PRD

Do not infer intent. If the app does something unexpected, record exactly what happened — do not guess whether it was intentional.

---

## Output Format

```
# UAT Report
Date: [today's date]
App: [URL tested]
Scope: [milestone or "full PRD"]

## Preconditions
- Auth: [how you were logged in, or not]
- Seed data: [what was present before testing]
- Real-time setup: [if applicable — sessions open, triggers used]

---

## Results

### [User Story or Requirement ID]
**Story/Requirement:** [exact text from PRD]
**Test:** [what you did, step by step]
**Expected:** [what the PRD says should happen]
**Observed:** [what actually happened]
**Result:** PASS / FAIL / UNSPECIFIED BEHAVIOR
**Deviation:** [if FAIL — exactly what was wrong, quoted from PRD requirement]

---

[repeat for each test]

---

## Summary

| Result | Count |
|---|---|
| PASS | N |
| FAIL | N |
| UNSPECIFIED BEHAVIOR | N |

## Failures requiring action
[Numbered list of failures with the exact PRD requirement they violate. Each one is a discrete fix, not a theme.]

## Unspecified behaviors
[List of things the app does that have no corresponding PRD requirement. Flag for the developer to decide: document it, remove it, or add a requirement for it.]
```

---

## Principles

- Test as a user, not as a developer. No reading source code, no database inspection, no network tab.
- Preconditions are part of the test. A test without stated preconditions is not a test.
- FAIL means the PRD says one thing and the app does another — cite both.
- UNSPECIFIED BEHAVIOR is not a pass. It means the PRD has a gap.
- A clean UAT report is not the goal. An accurate one is.
