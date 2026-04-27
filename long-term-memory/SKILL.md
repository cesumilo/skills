---
name: long-term-memory
description: Maintains a persistent project journal in MEMORY.md across sessions. Records state, architectural decisions, discoveries, task history, and open questions; reconciles with the project's planning documents; archives old entries automatically. Invoke at session start and after every completed task. Toggle with "memory on" / "memory off". Compatible with pair-mode.
---

# Long-Term Memory Management

The agent maintains a persistent **long-term memory** across sessions via a dedicated file: **`MEMORY.md`**. This file is the agent's journal — a durable record of what has been built, what has been learned, and what decisions were made (and why). It complements whatever product/planning documents the project uses by capturing the **history and discoveries** of the project.

Memory management is **fully autonomous and ON by default**: the agent reads, updates, reconciles, and archives `MEMORY.md` on its own initiative. The first-run bootstrap requires explicit user approval; everything after that is automatic until the user disables the mode.

## 🎚️ Memory Toggle

Memory is **ON by default**. The user can change state via:

- `memory off` — disable writes (reads still happen for context).
- `memory on` — re-enable writes.
- `memory status` — report current state.

When **OFF**:

- The agent still **reads** `MEMORY.md` at session start for grounding.
- The agent does **NOT** write, update, reconcile, or archive.
- If `MEMORY.md` exists and memory is OFF, the agent reminds the user **once per session**: *"📓 Memory is OFF; `MEMORY.md` exists but I won't update it. Say `memory on` to resume journaling."*

State changes are announced explicitly:

> 📓 **Memory: ON** — I'll journal autonomously at task boundaries.
> 📓 **Memory: OFF** — I'll read `MEMORY.md` for context but won't write.

## 📂 Project Context Discovery

This skill is **agnostic** about which files describe the project's purpose and plan. At session start, the agent scans the project root (and one level deep) for files matching common conventions:

- **Product/spec:** `PRODUCT.md`, `README.md`, `SPEC.md`, `VISION.md`, `docs/product.md`.
- **Plan/roadmap:** `PLAN.md`, `ROADMAP.md`, `TODO.md`, `BACKLOG.md`, `docs/plan.md`.
- **Architecture:** `ARCHITECTURE.md`, `ADR/`, `docs/architecture.md`.

Whichever exist become the source of truth for product intent and plan. If none exist, the agent asks the user to point at relevant docs or confirm there are none. Throughout this skill, **"the plan document"** and **"the product document"** refer to whatever the project actually uses.

## 📂 The `MEMORY.md` File

`MEMORY.md` lives at the project root. It is structured as an append-mostly log:

```markdown
# Project Memory

## 📌 Current State Summary

A short (5–10 lines) snapshot of where the project stands right now. Rewritten in place after each completed task.

## 🏛️ Architectural Decisions

Durable decisions that shape the codebase. Append-only. Format: ADR-style, with a stable ID for cross-referencing.

- **ADR-001 — [One-line title]**
  - **Context:** …
  - **Decision:** …
  - **Consequences:** …

## 🔍 Discoveries & Learnings

Non-obvious findings uncovered during implementation. Each entry has a stable ID for cross-reference.

- **DL-001 — YYYY-MM-DD — [short title]:** …

## 📜 Task Log

Chronological record of completed tasks. Append-only. Format:
`YYYY-MM-DD — [Task] — [Outcome] — [Files touched]`

## ❓ Open Questions & Follow-ups

Things noticed but not addressed. Moved to "Resolved" or deleted once handled.
```

**Recommendation:** commit `MEMORY.md` to version control — it is durable project history valuable to humans too. `MEMORY.archive.md` may optionally be `.gitignore`d. The agent does **not** modify `.gitignore` autonomously.

## 🌱 First-Run Bootstrap

The first time the agent operates in a project, `MEMORY.md` will not exist. Before doing any other work:

### Step 1: Detect

After attempting to read `MEMORY.md`, if missing:

1. Announce: `🌱 No MEMORY.md found — bootstrapping long-term memory from [discovered files].`
2. Do NOT proceed with any task until bootstrap completes or the user declines.

### Step 2: Gather

Read the discovered product/plan/architecture documents in full and extract:

- Project's purpose in 1–2 lines.
- Core domain concepts (for ubiquitous-language grounding).
- Already-accepted constraints.
- Current phase, next pending task, and any documented architectural decisions.

If documents are missing or too sparse, ask the user concise targeted questions before drafting.

### Step 3: Draft

Compose an initial `MEMORY.md` using the section structure above. The header notes which files seeded it, e.g. `Bootstrapped from README.md and ROADMAP.md on YYYY-MM-DD.` Pre-existing decisions become `ADR-001…`.

### Step 4: Approve

Show the draft to the user and ask for explicit approval. Acceptable responses: `looks good`, `approve`, edits, or `skip`. On `skip`, set memory to OFF for the session and proceed.

## 🔄 Session-Start Protocol

At every session start (after bootstrap):

1. Read `MEMORY.md` in full.
2. Skim **Discoveries & Learnings** so known gotchas surface during the upcoming work — cite `DL-NNN` if a current task touches one.
3. Recap to the user.
   - **Default:** 2–3 lines covering current state, last task, next intended step.
   - **If the Current State Summary is unchanged from last session's end and recent:** a one-line acknowledgement suffices (e.g. *"📓 Memory loaded — picking up at task 3.2."*).
4. Stash an in-memory copy of the file's contents and its `mtime`/hash for concurrent-edit detection (see below).

## ✍️ Update Protocol

Memory is updated **at task boundaries**. A task is "complete" when:

- The user confirms it, OR
- The working agreement's definition of done is met (e.g. tests green in TDD projects).

When in doubt, ask.

### Concurrent-Edit Safety

**Before every write**, re-read `MEMORY.md` and compare against the stashed copy. If it changed:

1. STOP. Do not write.
2. Show the user a diff of their changes vs. the agent's intended update.
3. Ask how to merge. Resume only after explicit direction.

### What to record

- **Task Log:** always append one entry per completed task. Cap files-touched at **5**; for more, summarize (e.g. `12 files across src/auth/`).
- **Current State Summary:** rewrite in place to reflect new reality.
- **Discoveries & Learnings:** append entries that meet the heuristic below.
- **Architectural Decisions:** append a new ADR (with next sequential ID) only for durable, hard-to-reverse choices.
- **Open Questions:** append anything noticed but deferred; resolve or remove stale items proactively.

### Discovery Heuristic

- ✅ **Record** anything that surprised you, contradicted docs, or would cost the next person >15 minutes to rediscover.
- ❌ **Skip** standard library behavior, things already in product/plan docs, or things obvious from reading the code.
- 🤔 **Borderline:** ask the user once early in the project; calibrate from their answer.

## 🔗 Reconciliation with the Plan

After updating memory, scan the plan document for drift and propose (do **not** auto-apply) reconciliations:

| Situation | Action |
|---|---|
| Plan task done but unmarked | Suggest marking it done. |
| Discovery invalidates a plan assumption | Flag the affected task; suggest a plan update. |
| New ADR conflicts with the plan | Surface the conflict; propose plan revision. |
| Renamed/moved task (same work, different name) | Ask the user to confirm equivalence; normalize naming in `MEMORY.md` going forward. |
| Open Question now answered | Move it to Resolved or delete it. |

The user owns all changes to the plan document.

## 🗄️ Archiving Policy

After each update, check thresholds. Archive if **any** holds:

- File exceeds **500 lines**, OR
- Task Log exceeds **50 entries**, OR
- Oldest Task Log entry is more than **90 days old**.

When triggered:

1. Create or append to `MEMORY.archive.md` at the project root.
2. Move the **oldest half** of Task Log and Discoveries & Learnings (preserving chronological order and IDs).
3. **Never archive** Architectural Decisions, Current State Summary, or Open Questions.
4. Leave a breadcrumb: `📦 Entries before YYYY-MM-DD archived to MEMORY.archive.md.`
5. Announce the archive operation to the user.

### Surfacing Archived Context

When the agent encounters an unfamiliar domain term or decision not present in live `MEMORY.md`, it **searches `MEMORY.archive.md`** before asking the user. If found there, it cites the archived ID and (if still relevant) suggests promoting a brief note back into the live file.

## 📢 Announcing Memory Operations

Every memory-affecting action is announced compactly. Example:

> ✅ Task complete: `UserProfileUpdated` event implemented.
>
> 📝 **Memory updated**:
>
> - Task Log: +1 entry (3 files touched)
> - Discoveries: +1 entry (DL-014: event-sourcing library requires `@EventHandler` on async handlers)
> - Current State Summary: rewritten
>
> 🔗 **Reconciliation**: plan task `2.3` still marked pending — suggest marking it done.
>
> 🗄️ **Archive check**: 312 lines, 23 Task Log entries, oldest 41 days old. No archiving needed.

## 🤝 Interaction with Pair Programming Mode

When `pair-mode` is active, memory still works, with adaptations:

- Task Log entries credit the user: `YYYY-MM-DD — [Task] — ✅ Done (user-implemented, agent-guided) — [files]`.
- Discoveries surfaced by the user are tagged `(noted by user)`.
- The agent does **NOT** silently update memory mid-pair-session — updates happen at task boundaries, announced above, so the user owns the narrative.
- **On `pair off`:** before pausing for user direction (per pair-mode protocol), flush a single memory update summarizing the pair session — one Task Log entry plus any discoveries — then wait for the user's next instruction.

## 📝 Writing Strategy

Prefer chunked, append-style writes over full-file rewrites where the host platform supports it. Always re-read before writing (see Concurrent-Edit Safety). When a full rewrite is necessary (e.g. updating Current State Summary), do so atomically and in a single operation.

