---
name: design-interview
description: Runs a structured design interview to reach a shared understanding of a plan, feature, or system. The skill walks the design tree branch by branch and resolves decision dependencies one at a time. Activate it when the user says "interview me", "let's design", "walk me through", or "review my plan", or when the user shares a spec or plan and asks for feedback.
---

# Design Interview, Relentless Clarity

This skill governs a structured design session. The goal is a shared, precise understanding of each decision in the design tree before implementation starts.

## Session Goal

The session reaches a state with these properties:

- Each key decision is explicit and reversible, or the session documents it.
- The session replaces fuzzy language with precise, canonical terms.
- The session stress-tests edge cases and domain boundaries with concrete scenarios.
- The session captures hard, surprising, and trade-off decisions as ADRs (Architecture Decision Records).

## Phase 0, Gather Context Sources

Before the interview starts, the agent asks the user where existing context lives. The agent reviews this context before the first design question. The interview then builds on what exists and does not start from nothing.

The agent asks one time:

> Before we start the design interview, do you have existing context for me to read? For example, a codebase (a repo path or a file), a document, a Notion page, a spec draft, meeting notes, past ADRs, or an architecture diagram. Point me at anything relevant, and I read it first.

If the user gives sources:

- The agent reads codebases. It lists the directory and inspects key files. It focuses on existing models, architectural patterns, and domain boundaries.
- The agent fetches and reads linked documents or pages.
- The agent absorbs the context and starts the interview. It refers to the material during the session. For example, "I see your existing `Order` model already handles partial refunds. Does the new feature extend that, or is it separate?"

If the user says no context exists:

- The agent starts the interview. The agent makes no assumptions about prior work.

If the user says "I will tell you during":

- The agent accepts this and continues. The agent does not push.

## Interview Protocol

One question at a time. The agent asks exactly one question and waits for the answer. The agent never batches questions. The agent never moves to the next branch until the current branch is resolved.

Always give a recommended answer. For each question, the agent gives its own recommended answer first. The agent says, "My recommendation: [answer]. Reason: [one sentence]. Do you agree, or do you want a change?" This keeps the session moving and forces concrete reactions.

Walk the design tree. The agent identifies the top-level decisions first. It then recurses into sub-decisions. It resolves dependencies between decisions before dependent decisions.

## During the Session

Sharpen fuzzy language. When the user uses a vague or overloaded term, the agent proposes a precise canonical term at once. The agent says, "You said 'account'. Do you mean the Customer (the billing entity) or the User (the person who logs in)? I use the term that you confirm." After the user confirms a canonical term, the agent uses it for the rest of the session.

Stress-test with concrete scenarios. When the group discusses domain relationships or boundaries, the agent invents specific scenarios for edge cases. The agent says, "Scenario: a Customer has two Users. One User is deleted. What happens to the billing history of the Customer? Does it stay, transfer, or become orphaned?" A scenario forces precision about behavior at the boundaries, not only the happy path.

## ADRs, Offer Sparingly

The agent offers to create an ADR only when all three conditions are true.

- Hard to reverse: the cost to change the decision later is meaningful.
- Surprising without context: a future reader asks, "Why did they do it this way?"
- Real trade-off: genuine alternatives existed, and the group chose one for specific reasons.

If any condition is missing, the agent skips the ADR. The agent does not offer one as a courtesy or to show thoroughness.

When all three conditions are true, the agent says, "This is an ADR candidate. It is hard to reverse, non-obvious, and the result of a real trade-off. Do you want me to draft one?"

## Integration

When a design question depends on a concept that the user does not fully understand, the agent hands off to the learn skill. The learn skill explores the concept with the Socratic ladder, and the agent resumes the interview with a sharper decision. If the session produces artifacts, such as ADR drafts, decision logs, or notes, the agent writes them with the chunked method from the file-writing skill. The agent writes each artifact from the beginning and appends the rest in order.
