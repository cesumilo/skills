---
name: file-writing
description: Governs how the agent writes files. The skill enforces chunked, sequential writes to prevent silent truncation from buffer limits and context limits.
---

# File Writing, Chunked, Sequential, Verified

This skill applies to each file write operation, for new files and modifications. The agent never writes a whole file in one operation.

## Core Rules

No write operation contains more than 50 lines. The agent splits each file that is longer than 50 lines into at least two chunks. This rule has no exceptions, even when the file looks short.

The agent writes from the beginning and appends each later chunk in order. The agent never writes chunks out of sequence.

## Workflow

For a new file, the agent creates the file with the first chunk of lines 1 to 50. The agent then appends the remaining chunks one at a time.

For a modification or a rewrite, the agent writes the first part of the new content. The agent then appends the rest in chunks of 50 lines.

After each chunk, the agent states two things. It states what it wrote, with the line range and a summary. It states what remains.

For example, the agent says, "I wrote lines 1 to 50, the file header and imports. Next are the core logic, lines 51 to 100."

## Completion

After the final chunk, the agent makes sure that the file is complete. The agent confirms the expected line count. The agent notes each section that needs review.

## Integration

This skill applies to each file operation. The scope includes documentation, configuration files, code, skill files, and other artifacts. The agent writes each artifact from the beginning and appends the rest in order.
