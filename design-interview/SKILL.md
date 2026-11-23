---
name: design-interview
description: Runs a structured design interview to reach a shared, precise understanding of a plan, feature, or system before implementation starts. Walks the design tree one decision at a time, sharpens vague language into canonical terms, keeps GLOSSARY.md current, and captures hard trade-offs as ADRs. Use when the user says "interview me", "let's design", "walk me through", or "review my plan", shares a spec or plan and asks for feedback, or uses vague or overloaded terminology that needs a canonical definition.
---

# Design Interview, Relentless Clarity

This skill governs a structured design session. The goal is a shared, precise understanding of each decision in the design tree before implementation starts.

## Session Goal

The session reaches a state with these properties:

- Each key decision is explicit and reversible, or the session documents it.
- Each vague term has a precise, canonical name.
- Each agreed concept has an entry in the project `GLOSSARY.md`.
- Each edge case and domain boundary is stress-tested with concrete scenarios.
- Each hard, surprising, or trade-off decision is captured as an ADR (Architecture Decision Record).

## Session Flow

1. Gather context and read the project `GLOSSARY.md`.
2. List the top-level decisions.
3. Resolve one decision at a time. Recurse into dependent decisions only after the current decision is resolved.
4. Agree a canonical term for each vague term and record it in `GLOSSARY.md`.
5. Agree a name for each new concept and record it in `GLOSSARY.md`.
6. Capture decisions that meet the ADR test.

## Phase 0, Gather Context Sources

Before the interview starts, look for existing context in the environment (filesystem, tools, etc.). Review this context before the first design question. The interview then builds on what exists and does not start from nothing.

Find the project's artifact locations in this order:

1. Read the agent context file (`AGENTS.md`, `CLAUDE.md`, or equivalent) for documented locations. If it names them, follow it.
2. If it does not, check the common conventions: `docs/specs/`, `docs/adr/`, `docs/decisions/`, and `GLOSSARY.md`.
3. Confirm what you found with the user before relying on it.
4. Record the resolved locations in the glossary or session notes so the next session does not rediscover them.

If the project has a `GLOSSARY.md`, read it first. Its terms are binding for the session. Reuse them and propose new entries where gaps exist.

## Interview Protocol

Ask exactly one question and wait for the answer. Never batch questions. Never move to the next branch until the current branch is resolved.

For each question, state the question first. Then give a recommended answer. Never give the recommendation without the question.

```
Question: [the one question].

My recommendation: [answer].

Reason: [one sentence].
Do you agree, or do you want a change?
```

## During the Session

Sharpen fuzzy language. When the user uses a vague or overloaded term, propose a precise canonical term at once. Say, "You said 'account'. Do you mean the Customer (the billing entity) or the User (the person who logs in)? I use the term that you confirm." After the user confirms a term, use it for the rest of the session and record it in `GLOSSARY.md`.

Name every new concept. A new concept can be a feature, an architectural component, a domain entity, or a process. Do not leave it as description: agree one word or short phrase to name it. Say, "This thing that decides which warehouse ships an order. Do we call it the Fulfilment Router? I use that name from here on." Name the concept before building on it. Name it by its role, not by its class name.

Keep the glossary current. `GLOSSARY.md` at the repository root is the shared source of truth for what words mean in the project. If the project keeps documentation elsewhere, follow that convention and say where the file lives.

- Write on agreement, not at the end of the session. Update the entry when its definition changes.
- Read each new or changed entry back to the user so agreement is explicit.
- Check for an existing entry before defining a concept. Reuse it and never invent a second name for the same concept. One deliberate layer pair is allowed when the product and the code use different names for the same concept; record the mapping and mark it as intentional, not as a synonym.
- Correct collisions on the spot: "That is the Customer per the glossary. I keep using that term."
- Two concepts never share one word. Split them into two entries and ask the user to confirm the difference. If the same word must serve both concepts, record the decision as an ADR, regardless of the ADR conditions below.
- Define product and domain terms, not code identifiers. A class, function, or type name is already defined in the code. Add a code term only when it carries product meaning or is overloaded, and then define the concept, not the class.

Use one entry per concept, in alphabetical order, with the term, a precise project definition, and the words to avoid. See [references/glossary-format.md](references/glossary-format.md).

Stress-test with concrete scenarios. When discussing domain relationships or boundaries, invent specific scenarios for edge cases. Say, "Scenario: a Customer has two Users. One User is deleted. What happens to the billing history of the Customer? Does it stay, transfer, or become orphaned?" A scenario forces precision about behavior at the boundaries, not only the happy path.

## ADRs, Offer Sparingly

Offer to create an ADR only when all three conditions are true:

- Hard to reverse: the cost to change the decision later is meaningful.
- Surprising without context: a future reader asks, "Why did they do it this way?"
- Real trade-off: genuine alternatives existed, and the user chose one for specific reasons.

If any condition is missing, skip the ADR. Do not offer one as a courtesy or to show thoroughness.

When all three conditions are true, say, "This is an ADR candidate. It is hard to reverse, non-obvious, and the result of a real trade-off. Do you want me to draft one?"

Write the ADR where the project keeps them. If the location is undocumented, ask before you choose one.

## Integration

When a design question depends on a concept the user does not understand, hand off to the `learn` skill and resume with a sharper decision.
