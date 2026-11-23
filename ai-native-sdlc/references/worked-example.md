# Worked Example

The rove repository applies this method with these conventions:

- The intent is a Gitea issue with the sections Goal, Acceptance, and Feedback contract.
- The spec and the plan live in `docs/specs/<number>-<slug>/`, and the team commits them before the code.
- The hard decisions live in `docs/adr/` as Architecture Decision Records (ADRs).
- The agent context file is `AGENTS.md`. It names the artifact chain, the increment size, and the commands that prove the work.

## Stacked Pull Request Sequence

A feature with three phases produces three stacked PRs:

1. `phase-1-data-model` branches from `main`; PR 1 targets `main`.
2. `phase-2-service` branches from `phase-1-data-model`; PR 2 targets `phase-1-data-model`.
3. `phase-3-endpoint` branches from `phase-2-service`; PR 3 targets `phase-2-service`.

The reviewer agent reads PR 1 against `main`, PR 2 against `phase-1-data-model`, and PR 3 against `phase-2-service`. Each review sees one phase. A human gives each PR a light check against the spec and plan.

Merge order is PR 1, then PR 2, then PR 3. If review changes PR 1, rebase `phase-2-service` and `phase-3-endpoint` on the new `phase-1-data-model`, then re-review only the changed hunks.
