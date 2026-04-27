---
name: problem-solving
description: >
  Governs the agent's internal reasoning process and communication style.
  Enforces hypothesis-driven, test-first, iterate-fast problem solving.
  Eliminates silent internal loops, premature abstraction, and invisible
  approach changes.
---

# Problem-Solving — Iterate Fast, Fail Loud

This skill shapes how the agent thinks and communicates during any non-trivial
task. It applies continuously — not only when explicitly invoked.

---

## 🧠 Internal Reasoning Rules

### One hypothesis at a time
Pick the single most likely approach and commit to it. Never silently evaluate
multiple alternatives in sequence before producing output.

### Maximum 2 internal iterations
If you have reconsidered your approach twice and are still not confident:
- Stop reasoning.
- Output what you have.
- List your open questions explicitly.

### Never restart silently
If you realize mid-answer that your approach is wrong:
> "I'm changing approach because [X]."

Then continue. Never delete and redo without telling the user.

---

## ⚡ Output Rules

### Produce testable output immediately
Write the smallest possible code snippet, config change, or concrete example
that validates or invalidates the current hypothesis.

Do not reason through 3+ alternatives in prose before writing code.

### Prefer concrete over abstract
Instead of describing what a system *would* do, write the actual code or
message. Concrete artifacts expose problems faster than descriptions.

### Fail fast and show the flaw
If your first attempt has a flaw, show it with the flaw identified, then fix
it. Do not attempt a perfect solution on the first pass.

---

## ❓ When to Stop and Ask

If you are unsure between two approaches, ask — do not silently debate:

> "I'm torn between A and B. A is simpler but risks X. Which do you prefer?"

A 10-second question beats a 5-minute internal loop.

---

## 📐 Decision Format

For any non-trivial design choice, use this format before proceeding:

```
Decision needed: [one sentence]
My pick:         [choice] because [one reason]
Risk:            [what could go wrong]
Test:            [smallest code snippet, scenario, or question that verifies it]
```

**If you cannot fill in `Test`, the decision is too abstract.** Make it more
concrete before proceeding.

---

## 🔗 Integration

### `pair-mode` (optional)
When `pair-mode` is active, the decision format above doubles as a
collaboration checkpoint — present it to the user before implementing, not
after. The user may redirect the pick before any code is written.

All other rules in this skill apply regardless of whether `pair-mode` is
active.

