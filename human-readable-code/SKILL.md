---
name: human-readable-code
description: Enforce strict visual rhythm, line-breaking, flat control flow, and natural naming so code reads cleanly for humans.
---

# Human-Readable Code Skill

Write code for the human eye first, not just the compiler. Every file should be effortless to scan top-to-bottom without horizontal scrolling, mental backtracking, or deciphering dense one-liners.

## 1. Line Breaks & Visual Rhythm
- **80-Character Soft Wrap:** Break lines around 80 characters (never exceed 100). A human eye should never have to scroll horizontally to read a statement.
- **One Idea Per Line:** Never put two statements on the same line, and never pack multiple operations into a single dense expression.
- **Vertical Lists for Long Calls:** When a function call, array, or object has more than 3 items—or exceeds the line limit—put every item on its own indented line.
- **Stacked Method Chains:** When chaining 2 or more method calls, put each call on a new indented line so each transformation step stands out clearly.
- **Paragraph Spacing (Blank Lines):**
  - Put a single blank line before and after every `if`, `for`, `while`, `switch`, and `try` block.
  - Put a single blank line before a final `return` statement when preceded by 2 or more lines of logic.
  - Group tightly related lines into 3–6 line "paragraphs" separated by a single blank line. Never use double blank lines.

## 2. Control Flow & Indentation
- **No One-Line `if` / Loops:** Never write `if (x) return y;` or `for (...) doWork();` on one line. Always place the body on a new line with braces `{}` (or proper block indentation).
- **Early Returns Over `else`:** Exit early for edge cases and errors at the top of a function. Never wrap the main logic inside an `else` block after a `return` or `throw`.
- **Flat Over Nested (Max 2 Indents):** Keep indentation shallow. If logic nests deeper than 2 levels, extract the inner block into a named helper function.
- **No Nested Ternaries:** Never nest ternary operators (`a ? b : c ? d : e`). Use standard `if-else` or `switch` blocks so each branch has its own line.
- **Extract Complex Conditions:** Never put more than 2 boolean checks inside an `if (...)`. Assign the condition to a clear boolean variable on the line above (`canPublishArticle = ...`).

## 3. Reading Order (Top-Down Narrative)
- **Stepdown Rule:** Organize files from high-level to low-level. Place public entry-point functions at the top and the private helper functions they call directly below them.
- **Declare Right Before Use:** Introduce variables on the line immediately before they are needed, not in a giant block at the top of a function.
- **Keep `try` Blocks Tiny:** Wrap only the exact 1–3 lines that can actually throw an error inside a `try` block so the reader immediately sees what might fail.

## 4. Human-Friendly Naming
- **Read Like English:** Name booleans as yes/no questions (`isExpired`, `hasAccess`, `canRetry`) and functions as clear actions (`sendInvoice`, `parseConfig`).
- **Spell Out Words:** Never shorten words to save keystrokes (`customerAddress` instead of `custAddr`, `messageCount` instead of `msgCnt`).
- **Avoid Mystery Numbers:** Replace raw numbers and repeated strings with descriptive constant names (`MAX_LOGIN_ATTEMPTS = 5` instead of `5`).
- **No Negated Booleans:** Prefer positive names (`isEnabled` instead of `isNotDisabled`) so conditionals don't create double negatives (`!isNotDisabled`).

## 5. Minimal, High-Signal Comments
- **Delete Redundant Comments:** Never write comments that repeat what the code already says (e.g., `// increment counter` above `counter++`).
- **Document Intent Only:** Use brief comments only to explain *why* a non-obvious decision or workaround exists.

## 6. Clear & Constant App State
- **Single Source of Truth:** Every piece of state lives in exactly one place. Never copy the same value into two variables; derive it instead (`isCartEmpty = items.length === 0`, not a second flag kept in sync by hand).
- **Name the States, Don't Scatter Flags:** Describe the app's status with one explicit state (`status = "loading" | "ready" | "failed"`) instead of several booleans (`isLoading`, `isError`, `isDone`) that can contradict each other.
- **Make Impossible States Impossible:** Model state so invalid combinations cannot exist (no `isLoading = true` together with `error = "..."`).
- **Constants Over Magic Values:** State values that never change are declared once as named constants or enums at the top of the file (`STATUS_READY`), never retyped as raw strings.
- **Immutable by Default:** Use `const`/`final`/`readonly`. Don't mutate existing values; create a new value with the change. Reassign only when there is no clearer alternative.
- **One Place to Change State:** Only a small, named set of functions may update state (`startLoading`, `finishLoading`, `failLoading`). Never change state from random places in the code.
- **Initial State Is Explicit:** Declare the starting value of all state in one visible block so a reader sees the whole shape of the app at a glance.
- **Avoid Hidden Global State:** Pass what a function needs as parameters instead of reading shared mutable globals, so the function's behaviour is readable from its signature.
- **Specific Values Become Constants Immediately:** When you introduce a specific, meaningful value (a color code like `#FF5733`, a retry count, a timeout, a URL, a size limit), declare it as a named constant right away, even if it is used only once today. Such a value is easy to retype elsewhere later, and then it lives in two places and the single source of truth is broken. This applies only to these specific, reusable values, not to every variable (loop counters, `0`, `1`, temporary results, and so on).

## 7. Follow the Language's Own Conventions
- **Match the Language, Not Your Habit:** The same idea is spelled differently in each language (`createUserEntry` in JavaScript, `create_user_entry` in Python or Rust, `CreateUserEntry` in Go or C# for public names). Always write names in the style that language's community expects, so the code looks familiar to anyone who knows it.
- **Project Rules Win:** Before writing code, look for the project's own convention and follow it, even if it differs from the language default. Check in this order: linter and formatter configs (`.eslintrc`, `.prettierrc`, `ruff.toml`, `pyproject.toml`, `rustfmt.toml`, `.editorconfig`, `.golangci.yml`), a `CONTRIBUTING` or style document, then the surrounding code in the same file and folder.
- **Fall Back to the Official Style Guide:** If the project has no rules, use the language's official or most widely accepted guide (PEP 8 for Python, Rust API Guidelines, Effective Go, Google style guides for Java, Swift API Design Guidelines, and so on).
- **Never Mix Styles in One Codebase:** Pick one convention per language and use it everywhere, including files, folders, constants, classes, and functions. Consistency matters more than which style was chosen.
- **Keep the Other Rules:** The language convention only decides the spelling of a name. The rules above still apply (full words, verb-first function names, yes/no boolean names).

## 8. Keep Secrets Out of the Code
- **Think Twice Before Writing a Secret:** Before typing a password, API key, token, private URL, or connection string into code, stop and ask: "Would it be a problem if this was pushed or shared?" If yes, it does not belong in the code.
- **Use a `.env` File (or the Platform's Secret Store):** Put the secret in a `.env` file and read it at startup (`process.env.API_KEY`, `os.environ["API_KEY"]`). In production, use the host's secret manager instead.
- **Never Commit the `.env`:** Add `.env` to `.gitignore` before creating it. Commit a `.env.example` with the variable names and fake values, so others know what to set.
- **Load Secrets in One Place:** Read each environment variable once, in a single config file or block, give it a clear constant name, and import that name everywhere else. Fail early with a clear message if one is missing.
- **Assume a Pushed Secret Is Compromised:** Once a secret has been committed or pushed, deleting it in a later commit is not enough because it stays in the history. Revoke it and create a new one.
- **Not Everything Is a Secret:** Public, non-sensitive values (a color code, a retry count) stay as named constants in the code. Only sensitive or environment-specific values go in `.env`.
