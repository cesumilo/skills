---
name: session-to-spec
description: >
  Distills the current session into a structured specification document
  (SPEC.md). Captures design decisions, canonical terms, edge cases, known
  weak points, open questions, and ADRs. Activate with "spec this", "create
  spec", "write the spec", "turn this into a spec", "specify this", or
  after a design-interview or premortem-this session concludes.
---

# Session-to-Spec — Distill the Session into a Spec

This skill takes the current conversation — whether a formal
`design-interview`, a `premortem-this` stress-test, or a standalone
feature discussion — and produces a structured `SPEC.md` in the current
working directory.

---

## 🎯 Session Goal

Produce a durable, reviewable `SPEC.md` that captures every key decision
and insight from the session so that no knowledge is lost and
implementation has a clear, precise target.

---

## 📥 Input Sources

### Design-interview sessions
Extract from the full interview: canonical terms, design tree decisions,
stress-tested scenarios, and any ADRs created or discussed.

### Premortem-this sessions
Premortem findings feed directly into the **Known Weak Points &
Remediations** section. If no premortem was run but unresolved risks
are detected in the session, add a note:

> "Consider running `premortem-this` on this spec before implementation."

### Standalone conversations
Any session where the user discusses a feature, system, or plan. The
skill distills whatever was discussed — filling sections as available and
marking absent sections as "(not discussed)".

---

## 📐 SPEC.md Structure

The generated file uses this fixed template:

| Section | Content |
|---|---|
| **Overview** | 1–2 sentence summary of what was designed |
| **Canonical Terms** | Glossary of sharpened terms agreed during the session |
| **Decisions** | Each resolved design decision with rationale |
| **Scenarios & Edge Cases** | Concrete scenarios that were stress-tested |
| **Known Weak Points & Remediations** | Risks, assumptions, and mitigations surfaced during the interview or premortem |
| **Open Questions** | Anything left unresolved |
| **ADRs** | References or inline copies of any ADRs created |

Each section gets a heading in `SPEC.md`. Sections with no content are
marked "(not discussed)" rather than omitted — this makes gaps visible.

---

## 🔁 Protocol

### Extraction method
The agent distills the spec from its own internal knowledge of the
session — what was discussed, what decisions were reached, what terms
were sharpened. No transcript parsing or tool-based extraction.

### Trigger phrases
Activate when the user says any of:
- "spec this"
- "create spec"
- "write the spec"
- "turn this into a spec"
- "specify this"

Also activate immediately after a `design-interview` or `premortem-this`
session concludes, or when the user invokes the skill by name.

### File location
Write `SPEC.md` to the current working directory. If a `docs/` or
`specs/` directory exists, offer to write there instead — but default to
the current directory. The filename is always `SPEC.md`.

### Auto-generation with review prompt
The skill composes the full `SPEC.md` and writes it to disk without
requiring per-section approval. After writing, prompt the user:

> "`SPEC.md` has been written with [N] decisions, [N] canonical terms,
> and [N] scenarios. Please review carefully and let me know if anything
> needs adjustment."

### Handling an existing SPEC.md

When `SPEC.md` already exists in the target directory, read it and
decide:

| Scenario | Action |
|---|---|
| The existing spec is for the **same feature/system** being discussed in this session (matching overview/title) | **Update** — merge new content from this session into the existing spec, preserving unrevisited content |
| The existing spec is for a **different feature/system** | **Warn** — "This appears to be a spec for [X], but this session is about [Y]. Overwrite anyway, or save as a new file?" |
| The skill **can't determine** the relationship | **Ask the user** — show a brief summary of the existing spec and ask which action to take |

### Update mode
When updating an existing spec:
1. Merge new session content into the relevant sections.
2. Identify sections from the prior spec that were **not revisited** in
   the current session.
3. Tell the user: "These sections weren't revisited in this session —
   keep or remove?" for each stale section before finalizing.

---

## 🔗 Integration

### `design-interview`
The primary upstream skill. Its structured output — canonical terms,
design tree decisions, edge-case scenarios, ADRs — maps directly into
`SPEC.md` sections.

### `premortem-this`
Premortem findings populate **Known Weak Points & Remediations**. If a
design-interview was run but no premortem followed, the skill prompts
the user to consider running one.

### `long-term-memory`
After writing `SPEC.md`, add a brief journal entry to `MEMORY.md`:

> "Created `SPEC.md` for [feature/system name] capturing [N] decisions,
> [N] canonical terms."

This preserves the spec's existence and key metrics in the project memory
file so future sessions can discover it.

### `file-writing`
When writing `SPEC.md`, follow the chunked sequential write protocol —
no more than 50 lines per write operation, append in order, verify after
each chunk.

### `tdd-workflow`
The decisions in `SPEC.md` become acceptance criteria for the first
failing tests when implementation begins.

### `pair-mode`
When `pair-mode` is active, the user reviews and approves the final spec
before it is written. All pair-mode communication rules apply.
