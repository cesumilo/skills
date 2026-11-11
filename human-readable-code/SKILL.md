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

