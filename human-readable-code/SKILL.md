---
name: human-readable-code
description: Applies readability rules to hand-written code: line rhythm, flat control flow, narrative order, naming, and comments. Defers to the project formatter for anything it can enforce. Use when writing, refactoring, or reviewing code for readability, or when a reviewer asks to make code easier to scan.
---

# Human-Readable Code

Write code for the human eye first. Every file should scan top-to-bottom without horizontal scrolling, mental backtracking, or deciphering dense one-liners.

## Precedence

The project formatter and linter win. Run them and accept their output. Apply this skill only where they cannot reach: naming, control flow, narrative order, comments. When two rules here conflict, clarity wins. Generated code, vendored code, and minified assets are out of scope.

## Language Scope

The rules assume a C-family language. Adapt to the language:

- Python: PEP 8 spacing wins, including two blank lines between top-level definitions.
- Go, Rust, and other languages with a standard formatter: let the formatter set wrapping and indentation.
- SQL, YAML, markup, and config: apply naming and narrative order only.
- Established short names (`i`, `err`, `ctx`, `req`, `res`, `db`, `id`, `url`) are idiomatic. Keep them.

## 1. Line Breaks and Visual Rhythm

- Soft wrap around 80 characters, hard limit 100. Break earlier if a line reads poorly.
- One idea per line. Do not pack several statements into one dense expression; split an unreadable expression across lines.
- Long calls: if a call, array, or object has more than 3 items, or the line overflows, put each item on its own indented line.
- Method chains: stack 3 or more calls, or any chain that overflows the line. Keep two-call chains inline (`user.getName().trim()`).
- Blank lines: one blank line before and after each control block, and before a final return preceded by logic. Group related lines into short paragraphs and do not stack blank lines beyond what the language style requires.

## 2. Control Flow and Indentation

- Bracketed bodies: put loop and `if` bodies on their own lines, even for one statement. In brace languages, use braces.
- Early returns over `else`: exit early on edge cases and errors. Do not wrap the main path in `else` after a `return` or `throw`.
- Flat over nested: prefer at most two indentation levels. Deeper logic usually wants a named helper; extract when it improves reading, not mechanically.
- No nested ternaries. Use if/else or switch so each branch has its own line.
- Extract complex conditions: if an `if` holds more than two boolean checks, assign the condition to a named boolean on the line above (`canPublishArticle = ...`).

## 3. Reading Order (Top-Down Narrative)

- Stepdown: put public entry points first, then the helpers they call directly below.
- Declare right before use: introduce a variable immediately before it is needed, not in a block at the top.
- Keep `try` blocks small: wrap only the lines that can actually throw, so the reader sees what may fail.

## 4. Human-Friendly Naming

- Read like English: booleans as yes/no questions (`isExpired`, `hasAccess`, `canRetry`); functions as clear actions (`sendInvoice`, `parseConfig`).
- Spell out words except for established short forms (`customerAddress`, `messageCount`; `id`, `url`, `ctx` are fine).
- No mystery numbers: replace raw numbers and repeated strings with named constants (`MAX_LOGIN_ATTEMPTS = 5`).
- Avoid negated booleans: prefer `isEnabled` over `isNotDisabled`.

## 5. Minimal, High-Signal Comments

- Delete comments that repeat the code (`// increment counter`).
- Document intent only: explain why a non-obvious decision or workaround exists.

## Integration

The `ai-native-sdlc` skill applies this style in its code-and-tests stage.
