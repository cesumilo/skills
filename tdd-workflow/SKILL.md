---
name: tdd-workflow
description: Enforces strict Test-Driven Development (Red-Green-Refactor) workflow for any language or framework. Use this skill whenever implementing new features, fixing bugs, or modifying behavior in code. Activates on requests like "implement", "add feature", "fix bug", "create function", or any code change that affects behavior.
---

# TDD Workflow Skill

You MUST follow the Red-Green-Refactor cycle strictly for every feature or behavior change. Do not skip steps. Do not write implementation before tests.

## The Cycle

### Step 1: 🔴 RED — Write a Failing Test First

- Before writing ANY implementation code, write a test that defines the expected behavior.
- The test MUST fail (or not compile) before proceeding.
- **Announce:** `🔴 RED: Writing failing test for [feature/behavior]`
- Run the test to confirm it fails. Show the failure output.

### Step 2: 🟢 GREEN — Write Minimal Code to Pass

- Write the **simplest possible** implementation that makes the test pass.
- No extra logic, no premature optimization, no "while I'm here" additions.
- **Announce:** `🟢 GREEN: Making the test pass with minimal implementation`
- Run the test to confirm it passes.

### Step 3: 🔵 REFACTOR — Improve the Code

- Clean up duplication, improve naming, extract abstractions—while keeping tests green.
- **Announce:** `🔵 REFACTOR: Improving [what you're improving]`
- Re-run tests after each refactor to ensure they remain green.

## Testing Conventions

Detect and follow the conventions already used in the project. If none exist, propose a sensible default and confirm with the user.

- **Test discovery:** Inspect the repo (config files, existing tests, package manifests) to identify the test runner, file naming convention, and directory layout. Match what's there.
- **Test structure:** Use the **Arrange-Act-Assert** pattern.
- **Test naming:** Use a descriptive format such as `[Layer/Module] > [Unit] - should [expected behavior] when [condition]`.
- **Test scope by layer:**
  - **Domain / pure logic:** Pure unit tests, no mocks needed.
  - **Application / use cases:** Mock external dependencies using simple stubs or fakes.
  - **Infrastructure / adapters:** Integration tests against real resources (in-memory implementations, test containers, or ephemeral instances).
  - **Presentation / API:** End-to-end tests using the framework's test client.
- **One behavior per test:** Each test should fail for exactly one reason.

### Generic Test Example (pseudocode)

```
test("Domain > Email - should create a valid email", () => {
    // Arrange
    const input = "user@example.com"

    // Act
    const email = Email.create(input)

    // Assert
    expect(email.toString()).toEqual("user@example.com")
})

test("Domain > Email - should reject invalid input", () => {
    expect(() => Email.create("invalid")).toThrow()
})
```

## 🔵 Post-Implementation Code Quality Checklist

After completing the 🟢 GREEN phase, before marking a task done, run through this checklist:

### 1. Magic numbers & strings → constants

- Extract recurring literal values into named constants grouped by purpose (e.g., status codes, error codes, status enums, configuration values).
- Use the language's idiomatic mechanism: `enum`, `const`, frozen object, module-level constant, etc.

### 2. Shared constants → single source

- If the same constant is defined in multiple files, consolidate it into one canonical module and re-export/import from there.
- Place each constant in the layer it logically belongs to (e.g., domain enums live in the domain layer; HTTP codes live in the presentation layer).

### 3. Search for remaining magic values

Grep the modified files for:

- Hardcoded domain strings (`"active"`, `"suspended"`, `"deleted"`, etc.).
- Bare numeric status codes (`401`, `403`, `500`, etc.).
- Hardcoded offsets or slice indices (`.slice(7)`, `substring(0, 10)`, etc.).
- Bare numeric timeouts/TTLs (`5000`, `300`, etc.).
- Hardcoded URL prefixes, header names, or environment keys.

### 4. Lint, format & typecheck

- Run the project's linter, formatter, and type checker on all modified files.
- Address all errors and warnings before declaring the task done.
- Run the full test suite once more to confirm nothing regressed.

### Refactor Example (pseudocode)

```
// ❌ Before
if (account.status == "suspended") { ... }
return response({ error: { code: "account_suspended" } }, 403)
cache.set(key, data, ttl=300)

// ✅ After
if (account.status == ACCOUNT_STATUS.SUSPENDED) { ... }
return response({ error: { code: ERROR_CODES.ACCOUNT_SUSPENDED } }, HTTP_STATUS.FORBIDDEN)
cache.set(key, data, ttl=AUTH_CONFIG.CACHE_TTL_SECONDS)
```

## Rules of Engagement

1. **Never write implementation before a failing test exists.**
2. **Never write more test code than needed to drive the next bit of implementation.**
3. **Never write more implementation than needed to pass the current failing test.**
4. **Always announce the phase** (RED, GREEN, REFACTOR) before doing the work.
5. **Always run tests after each phase** and report the result.
6. **If the user requests a change without a clear expected behavior**, ask for a concrete example so a test can be written.
7. **Match existing project conventions** for test framework, file layout, and assertion style. Do not introduce new tools without asking.

