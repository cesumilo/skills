---
name: file-writing
description: >
  Governs how the agent writes files. Enforces chunked, sequential writes
  to prevent silent truncation from buffer/context limits.
---

# File Writing — Chunked, Sequential, Verified

This skill applies to every file write operation — new files and modifications
alike. Never write an entire file in a single operation.

---

## 📏 Core Rules

### Chunk size
No more than **50 lines per write operation**.

### Always chunk
Even if a file appears short enough to write in one pass — if it exceeds
50 lines, split it into at least 2 chunks. No exceptions.

### Sequential order
Write from the beginning. Append each subsequent chunk in order. Never
write chunks out of sequence.

---

## 🔁 Workflow

### New files
1. Create the file with the first chunk (lines 1–50).
2. Append remaining chunks one at a time.

### Modifications / rewrites
1. Write the first portion of the new content.
2. Append the rest in successive 50-line chunks.

### After each chunk
Briefly state:
- What was written (line range + content summary).
- What remains.

Example:
> "Wrote lines 1–50: file header and imports. Next: core logic, lines 51–100."

---

## ✅ Completion

After the final chunk, verify the file is complete:
- Confirm expected line count.
- Note any sections that should be reviewed.

---

## 🔗 Integration

### `long-term-memory`
When writing or updating `MEMORY.md`, apply chunked writes. Prefer
append-style operations over full-file rewrites where possible, per the
memory skill's own writing strategy.

### All other file operations
This skill applies universally — docs, configs, code, skill files, and any
other artifact the agent produces.

