# Review with Agents

Review runs in two lanes: a focused reviewer agent and a light human approval. The lanes do different work; do not merge them.

## Reviewer Agent

The reviewer agent is not the author. It reviews each stacked PR against its own base branch, so it sees only that phase.

1. List every changed file in the PR. Review each one; do not sample.
2. Resolve the repository rules that apply to each file.
3. Read the diff and the surrounding code in the full file. Read related changed files when the change crosses a boundary.
4. Run the repository checks when the environment allows it.
5. Report each finding in a fixed shape:

| Field | Content |
|---|---|
| Location | `path/to/file:line` |
| Severity | `blocker`, `major`, `minor`, or `nit` |
| Finding | The defect in one sentence |
| Evidence | The code or rule that proves it |
| Fix | The suggested change, when short |

Severity in practice:

- `blocker`: data loss, security, broken build, or a violated hard rule.
- `major`: wrong behavior, missing test for changed behavior, or a design break.
- `minor`: maintainability problem with a clear fix.
- `nit`: taste. Omit unless a repository rule names it.

## Precision Before Recall

Report a finding only when you can point to the code and the rule or consequence it violates. Drop taste-based comments. A false positive costs a human triage cycle; a missed defect costs a later fix. In a light-review process, noise is the larger cost, so bias toward precision.

Consequences:

- Do not restate the diff.
- Do not comment on unchanged code unless the change makes it wrong.
- Do not invent a rule. If no rule applies, report the consequence instead.
- When unsure, label the finding `minor` and say what would confirm it.

## Human Lane

The human checks that the phase matches the intent, the spec, and the plan. The human approves or requests changes and does not re-review what the reviewer agent already proved. Branch protection enforces the approval, and the author agent never approves its own change.

## Stacked Pull Requests

Review from the bottom of the stack up. Each PR's diff contains one phase, so the reviewer reads one small change at a time. When a lower PR changes, restack the PRs above it and re-review only the changed hunks and the context they touch.

## Tooling

Alibaba Open Code Review (<https://github.com/alibaba/open-code-review>) implements this pattern. A deterministic pipeline selects files, bundles related files, matches rules per file, and positions comments, while an LLM agent reads repository context and writes line-level findings. It offers a native review agent and a delegation mode that runs the pipeline through the host coding agent.

The rules in this file are tool-agnostic. Any tool fits if it covers every changed file, applies per-file rules, and reports findings with file and line.
