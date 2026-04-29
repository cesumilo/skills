---
name: design-interview
description: >
  Runs a structured, relentless design interview to reach a shared understanding
  of a plan, feature, or system. Walks the design tree branch by branch,
  resolving decision dependencies one at a time. Activate when the user says
  "interview me", "let's design", "walk me through", "review my plan", or
  shares a spec/plan and asks for feedback.
---

# Design Interview — Relentless Clarity

This skill governs a structured design session. The goal is a shared,
precise understanding of every decision in the design tree before any
implementation begins.

---

## 🎯 Session Goal

Reach a state where:
- Every key decision is explicit and reversible or documented.
- Fuzzy language has been replaced with precise, canonical terms.
- Edge cases and domain boundaries have been stress-tested with concrete scenarios.
- Hard, surprising, trade-off decisions are captured as ADRs.

---

## 🔁 Interview Protocol

### One question at a time
Ask exactly one question, then wait for a response before continuing.
Never batch questions. Never move to the next branch until the current
one is resolved.

### Always provide a recommended answer
For every question you ask, provide your own recommended answer first:

> "My recommendation: [answer]. Reason: [one sentence]. Do you agree, or
> would you adjust?"

This keeps the session moving and forces concrete reactions rather than
open-ended thinking.

### Walk the design tree
Identify the top-level decisions first, then recurse into sub-decisions.
Resolve dependencies between decisions before moving to dependent ones.

---

## 🔬 During the Session

### Sharpen fuzzy language
When the user uses vague or overloaded terms, propose a precise canonical
term immediately:

> "You said 'account' — do you mean the **Customer** (the billing entity)
> or the **User** (the person logging in)? I'll use whichever term you
> confirm going forward."

Once a canonical term is agreed, use it consistently for the rest of the
session.

### Stress-test with concrete scenarios
When discussing domain relationships or boundaries, invent specific
scenarios that probe edge cases:

> "Scenario: a Customer has two Users. One User is deleted. What happens
> to the Customer's billing history? Does it stay, transfer, or become
> orphaned?"

Scenarios should force the user to be precise about behavior at the
boundaries — not just the happy path.

---

## 📋 ADRs — Offer Sparingly

Only offer to create an ADR when **all three** conditions are true:

| Condition | Question to ask yourself |
|---|---|
| **Hard to reverse** | Is the cost of changing this decision later meaningful? |
| **Surprising without context** | Will a future reader wonder "why did they do it this way?" |
| **Real trade-off** | Were there genuine alternatives, and was one chosen for specific reasons? |

If **any** condition is missing, skip the ADR. Do not offer one as a
courtesy or to signal thoroughness.

When all three are met:
> "This feels like an ADR candidate — hard to reverse, non-obvious, and
> the result of a real trade-off. Want me to draft one?"

---

## 🔗 Integration

### `pair-mode`
When `pair-mode` is active, this skill runs naturally within that
collaboration contract — the user drives scope and priority, the agent
drives precision and structure. All pair-mode communication rules apply:
no silent pivots, no unilateral decisions.

### `problem-solving`
Use the decision format from `problem-solving` when a design question
resolves into a concrete implementation choice:

```
Decision needed: [one sentence]
My pick:         [choice] because [one reason]
Risk:            [what could go wrong]
Test:            [smallest scenario or question that validates it]
```

This bridges design decisions directly into implementation decisions
without losing context.

### `tdd-workflow`
When the interview concludes and implementation begins, hand off directly
to `tdd-workflow`. Decisions made during the interview become the
acceptance criteria for the first failing tests.

### `long-term-memory`
After the session, key decisions — especially ADRs and canonical term
definitions — should be written to the project memory file per the
`long-term-memory` skill. This preserves the shared understanding across
sessions.

