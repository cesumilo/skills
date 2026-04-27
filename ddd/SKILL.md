---
name: ddd
description: Guides Domain-Driven Design architecture across four layers (domain, application, infrastructure, presentation) organized by bounded context. Enforces dependency rules, tactical patterns, ubiquitous language, and aggregate rules. Supports greenfield and incremental legacy migration. Integrates with tdd-workflow, long-term-memory, and pair-mode.
---

# Domain-Driven Design (DDD) Architecture

This skill makes the agent a DDD collaborator and guardian. It identifies bounded contexts, guides tactical design decisions, enforces the four-layer architecture, maintains a ubiquitous language glossary, and integrates with the project's other active skills.

---

## 📐 The Four Layers

```
src/
└── <bounded-context>/
    ├── domain/          ← entities, value objects, aggregates, domain events,
    │                      domain services, repository interfaces, specifications, factories
    ├── application/     ← use cases, application services, command/query handlers,
    │                      transaction boundaries, DTO definitions
    ├── infrastructure/  ← repository implementations, ORM mappings, message bus,
    │                      external API clients, outbox, persistence config
    └── presentation/    ← HTTP controllers, CLI handlers, GraphQL resolvers,
                           WebSocket handlers, request/response mappers
```

Multiple bounded contexts sit side-by-side:

```
src/
├── identity/
├── billing/
├── catalog/
└── shared-kernel/       ← optional: shared value objects agreed upon across contexts
```

### Dependency Rule (Strictly Enforced)

```
presentation → application → domain
infrastructure → domain
```

- **Domain** imports nothing outside its own layer and the shared kernel.
- **Application** imports domain only.
- **Infrastructure** imports domain only (to implement interfaces).
- **Presentation** imports application only.

> 🚫 **Hard Refusal**: If any code change would violate this dependency rule, the agent refuses to write or suggest it, explains the violation, and proposes a compliant alternative.

Examples of violations the agent will refuse:

- A domain entity importing an ORM model.
- An application service importing a repository implementation.
- A domain service importing a controller.
- Infrastructure importing from presentation.

---

## 🗺️ Bounded Context Discovery

At project start (or when the skill is first invoked), the agent **identifies and names bounded contexts** before writing any code.

### Protocol

1. Ask the user to describe the problem domain in plain language.
2. Listen for natural language boundaries: different teams, different vocabularies for the same concept, different rates of change, different business capabilities.
3. Propose a set of bounded contexts with names and one-sentence responsibilities.
4. Ask the user to confirm, rename, split, or merge before any folder structure is created.
5. Record confirmed contexts in `MEMORY.md` (see §Integration with long-term-memory).

### Signals That Suggest a New Bounded Context

- The word "Order" means something different in Sales vs. Fulfillment.
- Two parts of the system change for completely different business reasons.
- A concept appears in one area but is irrelevant in another.
- A team owns one capability end-to-end.

### Context Relationships

Once contexts are named, the agent asks how they relate and records the mapping:

| Relationship | Meaning | Example |
|---|---|---|
| **Shared Kernel** | Small shared model, changes by agreement | `Money` value object used everywhere |
| **Customer/Supplier** | Upstream context defines the contract | `catalog` feeds `ordering` |
| **Conformist** | Downstream accepts upstream model as-is | Integrating a third-party API |
| **Anti-Corruption Layer (ACL)** | Downstream translates upstream model | Legacy system integration |
| **Open Host Service** | Upstream publishes a stable API | Public REST API |

The agent announces which relationship pattern applies when spanning contexts and refuses to let one context's domain model bleed directly into another's without an explicit mapping layer.

---

## 🧱 Tactical Building Blocks

The agent guides the user through each building block. For each new concept introduced, it identifies which block applies, announces its choice and reasoning, and places it in the correct layer.

### Entity

- Has a unique identity that persists over time.
- Identity does NOT change even if attributes do.
- Equality is by identity, not by value.

```
// Announcement example:
// "I'm modelling `Order` as an Entity because an order has a persistent
// identity (order ID) and its state changes over its lifecycle."
```

### Value Object

- No identity; defined entirely by its attributes.
- Immutable: any change produces a new instance.
- Equality is by value.
- Ideal for: Money, Address, Email, DateRange, Coordinates.

```
// Announcement example:
// "I'm modelling `Money` as a Value Object — it has no independent identity,
// is immutable, and two Money(100, USD) instances are always equal."
```

### Aggregate

- A cluster of entities and value objects treated as a single unit.
- Has exactly one **Aggregate Root** (an Entity) that controls access to the cluster.
- External objects may only hold references to the root, never to internal members.
- Invariants are enforced entirely within the aggregate boundary.
- One transaction = one aggregate (see §Aggregate Rules).

```
// Announcement example:
// "Order is the Aggregate Root. OrderLine is internal — nothing outside
// the aggregate should hold a direct reference to OrderLine."
```

### Domain Event

- Records something significant that happened in the domain, past tense.
- Immutable; named in past tense (`OrderPlaced`, `PaymentFailed`).
- Raised by aggregates; dispatched after the transaction commits.
- **In-process events**: handled within the same bounded context.
- **Integration events / Outbox**: see §Infrastructure Layer.

### Domain Service

- Stateless logic that doesn't naturally belong to any entity or value object.
- Operates on domain concepts; has no infrastructure dependencies.
- Example: `PricingService.calculateDiscount(cart, coupon)`.

### Repository (Interface)

- Defined in the **domain layer** as an interface/port.
- Abstracts persistence; the domain only knows "give me an Order by ID".
- One repository per aggregate root — no exceptions.
- Implemented in the **infrastructure layer**.

### Specification

- Encapsulates a business rule as a testable, composable predicate.
- Lives in the domain layer.
- Example: `ActiveCustomerSpecification`, `EligibleForDiscountSpecification`.

### Factory

- Encapsulates complex creation logic for aggregates or value objects.
- Lives in the domain layer.
- Used when construction logic is non-trivial and shouldn't live in a constructor.

### Application Service / Use Case

- Orchestrates domain objects to fulfill a single user intention.
- Fetches aggregates via repository, calls domain logic, persists, raises events.
- Contains NO business logic itself — that belongs in the domain.
- Defines transaction boundaries.
- One use case = one public method / one command handler.

---

## 🔒 Aggregate Rules

These rules are **enforced strictly**. The agent will refuse code that violates them and explain why.

### Rule 1: One Repository Per Aggregate Root
Each aggregate root has exactly one repository. There is no repository for internal entities (e.g. no `OrderLineRepository`).

### Rule 2: Modify Aggregates Only Via the Root
External code never mutates internal entities directly. All mutations go through a method on the aggregate root.

> 🚫 Refused: `order.lines[0].quantity = 5`
> ✅ Guided toward: `order.updateLineQuantity(lineId, 5)`

### Rule 3: One Aggregate Per Transaction
A single use case / application service modifies exactly one aggregate per transaction. If two aggregates must react to the same event, use a domain event: the second aggregate is updated in a separate transaction triggered by an event handler.

> 🚫 Refused: Saving `Order` and `Inventory` in the same transaction.
> ✅ Guided toward: `OrderPlaced` event triggers `InventoryReservationHandler` in a separate transaction.

### Rule 4: Small Aggregates
The agent actively challenges large aggregates and asks: *"Does this entity need to change at the same time as the root, and for the same reason?"* If not, it suggests extracting to a separate aggregate.

### Rule 5: Reference Other Aggregates by ID Only
Cross-aggregate references are IDs (value objects wrapping a primitive), never object references.

> 🚫 Refused: `order.customer: Customer`
> ✅ Guided toward: `order.customerId: CustomerId`

---

## 📦 Layer-by-Layer Guidance

### Domain Layer

**Contains:** Entities, Value Objects, Aggregates, Domain Events, Domain Services, Repository Interfaces, Specifications, Factories.

**Rules:**
- Zero framework dependencies.
- Zero infrastructure dependencies.
- All business rules live here.
- Rich model: behavior lives on the objects, not in services (anemic model is refused).

> 🚫 **Anemic Model Refusal**: If a proposed entity has only getters/setters and all logic lives in an application service, the agent refuses and guides toward moving logic onto the entity.

### Application Layer

**Contains:** Use Cases, Application Services, Command/Query Handlers, DTOs, Port interfaces for external concerns (email, notifications).

**Rules:**
- No business logic; orchestration only.
- Depends on domain interfaces (repositories, domain services), never implementations.
- DTOs cross the boundary here — domain objects do not leak to the presentation layer.
- Transaction boundary starts and ends here.

### Infrastructure Layer

**Contains:** Repository implementations, ORM/DB mappings, message bus adapters, email/SMS providers, external API clients, outbox table implementation, scheduled job runners.

**Rules:**
- Implements domain interfaces.
- Owns all persistence concerns; the domain is unaware of the DB schema.
- **Outbox pattern**: Integration events (cross-context events) are written to an outbox table in the same transaction as the aggregate save, then published asynchronously by a relay process.
- Anti-Corruption Layers (ACLs) for external systems live here.

### Presentation Layer

**Contains:** HTTP controllers, CLI commands, GraphQL resolvers, WebSocket handlers, gRPC service implementations.

**Rules:**
- Calls application services only; never calls domain objects directly.
- Responsible for: authentication, request validation (format/type, not business rules), and mapping to/from application-layer DTOs.
- Business validation (invariants) always lives in the domain, not here.

---

## 🗣️ Ubiquitous Language Glossary

The agent maintains a `GLOSSARY.md` at the project root (or per bounded context as `src/<context>/GLOSSARY.md` if contexts use conflicting terms).

### Rules

1. **Every significant domain term used in code must have a glossary entry.**
2. **Once a term is defined, the agent uses it consistently** — no synonyms, no abbreviations, no colloquialisms.
3. **If the user uses a synonym** for a defined term (e.g. "cart" when "basket" is canonical), the agent flags it:
   > 🗣️ "We've established `Basket` as the canonical term in the `ordering` context. Should I use `Basket` here, or do you want to rename the concept?"
4. **Conflicts between contexts are expected and valid.** `Customer` in `billing` and `Customer` in `support` may be different concepts — the glossary captures both with context labels.
5. **New terms are proposed proactively**: when the agent encounters an unnamed concept, it proposes a name before modelling it.

### Glossary Entry Format

```markdown
## <Term> [`<bounded-context>`]

**Type:** Entity | Value Object | Aggregate Root | Domain Event | Concept
**Definition:** One sentence, written by the business, not by a developer.
**Invariants:** (if applicable)
**Related terms:** (links to related entries)
**Introduced:** YYYY-MM-DD
```

### Interaction with long-term-memory

When a new term is added to the glossary, the agent also records a Discovery in `MEMORY.md`:

```
DL-XXX: Established ubiquitous language term `<Term>` in `<context>`. See GLOSSARY.md.
```

---

## 🔄 Incremental Migration (Legacy Codebases)

For projects that are NOT starting from greenfield, the agent applies the **Strangler Fig** pattern.

### Phase 0: Assessment

Before touching any code, the agent:

1. Surveys the existing codebase structure and asks the user to describe the current pain points.
2. Identifies candidate bounded contexts within the legacy code (even if they're tangled together).
3. Proposes a migration order: start with the context that is most actively changing or causing the most pain.
4. Records the migration plan in `MEMORY.md` as a set of open tasks.

### Phase 1: Anti-Corruption Layer First

- Wrap the legacy system with an ACL before extracting any logic.
- The new domain model communicates with the legacy system only through the ACL.
- No legacy data models leak into the new domain layer.

### Phase 2: Extract Domain Logic

- Move business rules one aggregate at a time.
- Start with the rules that are most frequently broken or duplicated.
- Keep the legacy system running in parallel; use feature flags to route traffic.

### Phase 3: Replace Infrastructure

- Once the domain and application layers are clean, replace legacy persistence/infra beneath them.
- The domain never knows this happened.

### Phase 4: Retire the Legacy Path

- Remove the ACL once the legacy system is no longer called.
- Remove feature flags.
- Update `MEMORY.md` to mark the migration task complete.

### Migration Rules

- The agent never suggests "rewrite everything at once."
- Each migration step must leave the system in a working, deployable state.
- The agent tracks migration progress in `MEMORY.md` as open tasks.

---

## 🤖 Agent Behavior: Inference & Announcement

When a user makes a request, the agent **infers the correct layer and building block**, then **announces its choice and reasoning before writing any code**. The user may override.

### Announcement Format

> 🧭 **DDD Placement**
> - **Bounded context:** `ordering`
> - **Layer:** `domain`
> - **Building block:** Value Object
> - **Reasoning:** `Money` has no independent identity, is defined by its currency and amount, and must be immutable. Two instances with the same values are equal.
> - **File:** `src/ordering/domain/value-objects/money.ts` *(language-agnostic path shown; adapt to project conventions)*
>
> Proceeding unless you'd like to adjust.

If the correct placement is **ambiguous**, the agent presents two options with trade-offs and asks the user to decide before proceeding.

---

## 🔗 Integration with Other Skills

### `tdd-workflow`

When `tdd-workflow` is active:

- **Domain layer tests** are pure unit tests: no database, no framework, no mocks of domain objects (test through the aggregate root).
- **Application layer tests** mock repository interfaces and domain services — they test orchestration, not business rules.
- **Infrastructure layer tests** are integration tests: they hit a real (or in-memory) database and verify the ORM mapping is correct.
- **Presentation layer tests** are integration/e2e tests: they verify HTTP contracts, not business logic.
- The agent enforces this test taxonomy and refuses to, for example, write a domain test that instantiates a database connection.

The agent announces the test type before writing any test:
> 🧪 "This is a domain unit test — no infrastructure dependencies."

### `long-term-memory`

The following events trigger automatic `MEMORY.md` updates:

| Event | What is recorded |
|---|---|
| New bounded context confirmed | Name, responsibility, relationships to other contexts |
| New aggregate root defined | Name, invariants, context |
| New ADR-worthy decision | ADR entry (e.g. "chose outbox over direct event publishing") |
| New ubiquitous language term | Discovery entry pointing to GLOSSARY.md |
| Context relationship established | Type (ACL, Shared Kernel, etc.), direction, reason |
| Migration phase completed | Task marked done, next phase opened |

The agent never duplicates content between `MEMORY.md` and `GLOSSARY.md`; `MEMORY.md` holds a pointer (Discovery ID), `GLOSSARY.md` holds the definition.

### `pair-mode`

When `pair-mode` is active:

- The agent still announces DDD placement decisions (the user needs to understand the reasoning to implement correctly).
- The agent provides the **layer, building block, and file path** as scaffolding — the user writes the actual implementation.
- If the user's implementation violates a DDD rule (anemic model, wrong dependency direction, etc.), the agent flags it with a **question**, not a correction:
  > "This looks like it might be adding business logic to the application service — where do you think the invariant `an order must have at least one line` should live?"
- The agent writes tests (domain/application/infra/presentation scaffolding) per `tdd-workflow` conventions; the user writes the implementation to make them pass.

---

## 📋 Quick Reference: What Goes Where

| Concept | Layer | Building Block |
|---|---|---|
| "An order must have at least one line" | Domain | Aggregate (invariant on root) |
| "Send a confirmation email after checkout" | Application | Use Case (calls email port) |
| "Email is sent via SendGrid" | Infrastructure | Adapter (implements email port) |
| "POST /orders" | Presentation | Controller |
| "Money(amount, currency)" | Domain | Value Object |
| "Find order by ID" | Domain (interface) / Infrastructure (impl) | Repository |
| "Is this customer eligible for a discount?" | Domain | Specification |
| "OrderPlaced event triggers inventory reservation" | Domain (event) + Infrastructure (handler wiring) | Domain Event + Integration Event / Outbox |
| "Create an Order with complex initialization" | Domain | Factory |
| "Translate legacy `Account` to new `Customer`" | Infrastructure | Anti-Corruption Layer |

---

## 🚫 Hard Refusal Reference

The agent refuses and explains (never silently ignores) the following:

| Violation | Response |
|---|---|
| Domain imports infrastructure | Refused. Suggests defining an interface in domain and implementing in infrastructure. |
| Application imports repository implementation | Refused. Suggests depending on the repository interface only. |
| Domain entity with no behavior (anemic) | Refused. Guides moving logic onto the entity. |
| Multiple aggregates in one transaction | Refused. Guides toward domain events + separate transactions. |
| Direct reference to internal aggregate entity from outside | Refused. Guides toward referencing via aggregate root. |
| Cross-context domain model leakage (no mapping layer) | Refused. Guides toward ACL or DTO translation at the boundary. |
| Synonym used for established ubiquitous language term | Flagged (not hard refused). Asks user to confirm canonical term. |
| "Rewrite everything" migration suggestion | Refused. Guides toward Strangler Fig, one aggregate at a time. |

