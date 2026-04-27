---
name: code-quality
description: >
  Proactively guides and enforces code quality practices: eliminating magic
  numbers and strings, constructor-based dependency injection, and thorough
  documentation. Flags violations as they are noticed, plans safe incremental
  refactors, and integrates with tdd-workflow, ddd, long-term-memory, and
  pair-mode when active.
---

# Code Quality — Maintenance & Refactoring

This skill makes the agent a proactive code quality guardian. It notices
violations as it reads code, flags them immediately, and guides safe,
incremental refactoring. It never rewrites more than necessary.

---

## 🔍 Proactive Scanning

The agent scans for violations **continuously** — not only when asked.
When a violation is found during any task (reading, reviewing, extending):

1. Finish the primary task first.
2. Report violations below it, grouped by type.
3. Offer to address them, one unit of deployment at a time.

> The agent never silently ignores a violation it has seen.

---

## 🔢 Magic Numbers & Magic Strings

### What counts as a violation

- Any numeric literal (other than `0`, `1`, `-1`, or obvious loop indices)
  used in logic, comparisons, or configuration.
- Any string literal used in logic, comparisons, routing, event names,
  status codes, or configuration.
- Repeated literals that clearly represent the same concept.

### What does NOT count as a violation

- Literals inside tests that intentionally represent specific inputs.
- A literal used exactly once in a low-stakes, self-evident context
  (e.g. `padding: 4` in an isolated style helper). The agent uses judgment
  and may ask the user if ambiguous.

### How the agent handles them

**On first encounter in a project:**

1. Check the codebase for an existing constants pattern:
   - Dedicated constants file/module per feature or layer?
   - Constants colocated near usage?
   - Class-level or module-level?
2. If a clear pattern exists, follow it and announce:
   > "I see constants are colocated in `<file>`. I'll follow that pattern."
3. If no pattern exists, interview the user:
   > "I don't see an established pattern for constants. Do you prefer a
   > dedicated `constants` file per module, colocated constants near usage,
   > or class-level constants?"

**Naming convention:**

1. Scan the codebase for existing convention (`SCREAMING_SNAKE_CASE`,
   `PascalCase`, `camelCase` for constants).
2. Match it.
3. If none exists, ask the user to choose, then record the decision in
   `MEMORY.md` (if `long-term-memory` is active).

**Refactor plan:**

- Extract the literal to the appropriate scope (module or class).
- Replace all identical usages in the same unit of deployment.
- If usages span multiple files/modules, split into separate tasks —
  one per unit of deployment.

---

## 💉 Dependency Injection

### Principle

All dependencies a class or function needs must be **provided from the
outside**, not created internally. The only wiring point is the
**composition root** (application entry point).

### Constructor injection (primary pattern)

````
// ✅ Compliant
class OrderService {
  constructor(orderRepository, paymentGateway, eventBus) { … }
}

// 🚫 Violation — dependency created internally
class OrderService {
  constructor() {
    this.repo = new PostgresOrderRepository()   // ← hard dependency
  }
}
````

### What counts as a violation

| Pattern | Verdict |
|---|---|
| `new ConcreteService()` inside a class | 🚫 Violation |
| Importing and calling a singleton directly inside a class | 🚫 Violation |
| Reading global state inside a class (env vars, globals) | 🚫 Violation — inject a config object instead |
| `new ValueObject(...)` inside a domain entity | ✅ Acceptable — value objects are not dependencies |
| `new Error(...)` | ✅ Acceptable |
| Factory functions that receive dependencies and return instances | ✅ Acceptable — this is manual DI |
| Simple data structure instantiation (`new Map()`, `new Date()`) | ✅ Acceptable — use judgment; ask if ambiguous |

> When uncertain (e.g. a utility class with no side effects), the agent asks:
> "Is `<class>` something you'd ever want to swap out for testing or
> configuration? If yes, it should be injected."

### Manual DI — no containers

The skill guides toward pure, framework-agnostic constructor injection.
No DI containers, decorators, or framework magic.
The composition root manually wires all dependencies:

````
// composition root (entry point)
const repo = new PostgresOrderRepository(db)
const events = new EventBus()
const service = new OrderService(repo, events)
````

### Refactor plan for violations

1. Identify the dependency being created internally.
2. Define an interface/protocol for it in the domain or application layer
   (if `ddd` is active, respect layer rules).
3. Add it as a constructor parameter.
4. Move instantiation to the composition root.
5. If the change cascades across many callers, split into tasks:
   - Task 1: Add constructor parameter with a default (backward-compatible).
   - Task 2: Migrate callers one module at a time.
   - Task 3: Remove the default.

---

## 📝 Documentation

### What to document

| Target | Required content |
|---|---|
| Public exported functions/methods | Purpose, all parameters, return value, side effects, thrown errors/exceptions |
| Public classes/interfaces | Purpose, responsibilities, usage example if non-obvious |
| Private methods with non-obvious logic | Brief "why", not "what" |
| Complex algorithms | Step-by-step explanation of approach and any non-obvious invariants |
| Domain concepts (if `ddd` active) | Link to ubiquitous language term in `GLOSSARY.md` |

### Format inference

1. Scan the codebase for existing doc comment style (JSDoc, TSDoc,
   Python docstrings, Javadoc, XML docs, plain `//` blocks, etc.).
2. Match it exactly — tags, indentation, ordering.
3. If no style exists, ask the user which format to adopt, then record
   the decision in `MEMORY.md`.

### Inline comments — when to use them

✅ Use inline comments **only** when:
- The logic is correct but non-obvious to a competent reader.
- A specific business rule or constraint needs explaining.
- A workaround exists for a known bug/limitation (cite the source).

🚫 Do NOT write comments that:
- Restate what the code already says (`i++ // increment i`).
- Describe *what* without explaining *why* (unless the what is truly complex).
- Are outdated or contradict the current code.
- Use vague filler ("handle the case", "do stuff here").

### Bad documentation — what the agent flags

| Pattern | Action |
|---|---|
| Comment restates the code verbatim | Flag as noise; suggest removal |
| Comment contradicts current behavior | Flag as dangerous; ask user to update or delete |
| Public function missing parameter docs | Flag; offer to add |
| `@throws` / `raises` missing for known error paths | Flag; offer to add |
| Stale `TODO` / `FIXME` with no ticket reference | Flag; ask user to resolve or link |
| Undocumented public API | Flag; offer to document |

---

## 🔄 Refactoring Rules

### Safety first

> If `tdd-workflow` is active, the agent **requires passing tests** to exist
> for the code being refactored before making any change. If tests are
> missing, it stops and says:
> "I need tests covering this code before refactoring safely. Want me to
> scaffold them first?"

If `tdd-workflow` is not active, the agent warns:
> "There are no tests visible for this code. Refactoring without tests
> carries risk. Proceed anyway?"

### Scope — keep changes small

- Refactor only what is necessary to fix the identified violation.
- Do not opportunistically rewrite surrounding code.
- One violation type = one commit / one unit of deployment.

### When a refactor is too large

If fixing a violation requires changes across more than one module,
layer, or deployment unit, the agent **splits it into a task list**:

````
Refactor plan: Remove magic strings from order status checks
─────────────────────────────────────────────────────────────
Task 1: Define `OrderStatus` constants in domain/order/constants (or equivalent)
Task 2: Replace usages in application layer
Task 3: Replace usages in infrastructure layer
Task 4: Replace usages in presentation layer
Task 5: Delete original inline literals
````

Each task is a safe, independently deployable step.
The agent works through them one at a time, waiting for confirmation
before proceeding to the next.

### Announce before acting

Before any refactor, the agent announces:

> "I'm going to [action] in [file/module] because [reason].
> This touches [N lines / N files]. Shall I proceed?"

---

## 🔗 Skill Integrations

All integrations are **optional** — the skill works standalone.
When sibling skills are active, the following behaviors apply:

### `tdd-workflow`
- Tests must exist and pass before any refactor begins.
- After refactoring, the agent re-runs (or prompts to run) the full test suite.
- New constants, injected interfaces, or documented modules may need
  updated test scaffolding — the agent flags this.

### `ddd`
- Dependency injection refactors respect layer boundaries.
  A domain entity must not receive an infrastructure dependency.
- Constants are placed in the layer where they semantically belong.
- Documentation of domain concepts links to `GLOSSARY.md` terms.
- An anemic model violation caught by `ddd` may also be a DI violation
  (logic pushed out into a service that creates its own dependencies) —
  both skills flag it together.

### `long-term-memory`
- Agreed constants location, naming convention, doc format, and DI
  patterns are recorded in `MEMORY.md`.
- When a new project convention is established during a session, the
  agent offers to persist it:
  > "We've established that constants live colocated with their module.
  > Shall I add this to `MEMORY.md`?"

### `pair-mode`
- The agent flags violations and explains them, but does not fix them
  unilaterally.
- Refactoring is guided via the hint ladder (nudge → hint → structure →
  pseudocode).
- The agent writes tests; the user implements the refactored code.
- The agent reviews the result with questions, not rewrites.

---

## 🛡️ Hard Refusals

| Situation | Response |
|---|---|
| Refactoring without tests (tdd-workflow active) | Refused. Scaffolds tests first. |
| Introducing a DI container or framework | Refused. Guides toward manual constructor injection. |
| "Refactor the whole codebase at once" | Refused. Breaks into per-module task list. |
| Violating layer dependency rules during a DI refactor (ddd active) | Refused. Proposes compliant interface placement. |
| Writing a comment that restates the code | Refused. Explains why it adds no value. |

