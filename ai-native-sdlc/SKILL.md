---
name: ai-native-sdlc
description: Defines the AI-native SDLC artifact chain: intent, spec, plan, code and tests, pull request. Each stage commits a versioned artifact, each implementation phase ships as a stacked pull request, and a reviewer agent and a human review each PR. The agent commits and opens pull requests without asking. Use for feature work, refactors, and bug fixes in a repository that follows this method.
---

# AI-native SDLC, the Artifact Chain

This skill defines the working method for a repository that follows the AI-native software development life cycle (SDLC). The method replaces long handoffs between teams with a chain of versioned artifacts. You produce each artifact, commit it, and open the pull requests yourself. A human reviews at the checkpoints.

## The Core Rule

Every change moves through five stages in order. Each stage commits one artifact, and the next stage starts by reading that artifact.

1. Intent: the problem, the outcome, and the constraints.
2. Spec: the requirements and the design.
3. Plan: the files, the order of work, and the proof.
4. Code and tests: the implementation and its checks, one phase at a time.
5. Pull request: the review and the approval.

Commit each artifact and open each pull request without asking for permission. The commit history is the audit trail: it records who asked for the change, what you produced, and who approved it. Use clear commit titles. If the user asks you to stop short of committing, follow the user.

## Light Path

A trivial change may skip the spec and the plan. A trivial change touches one file, adds no behavior, and needs no new test, such as a typo, a comment, or a documentation fix. Record the intent in one line, such as the commit message, then open one pull request. When in doubt, run the full chain.

Do not use this method for exploratory spikes, throwaway scripts, or repositories that have not adopted it.

## Stage 1, Intent

The intent is the source of truth for the change. In most repositories the issue tracker holds the intent, and you read it first.

State five things: the problem, the proposed outcome, the affected users and systems, the constraints, and the open questions.

If the user gives an idea in conversation, write the intent to a file or an issue before any other work. Ask for missing facts; do not guess.

## Stage 2, Spec

The spec holds the requirements and the design in one document. Produce the spec from the intent in one session.

Apply the policies of the organization while you write the spec. The policies come from the policy skills present in the repository, such as security, compliance, and UX. Flag each area of concern, especially a conflict between two policies.

A human product owner reviews the spec against the intent. The spec must answer each open question from the intent or carry the question forward.

## Stage 3, Plan

No implementation starts without an approved plan. Write the plan before you change any code.

The plan states the files that change, the order of work, the risks, and the proof of success. The plan must be complete enough that a different engineer can do the work from the plan alone. Split the work into phases; each phase becomes one stacked pull request.

Test the plan before approval. Ask what the change can break, which step is the riskiest, and which options you rejected.

When the implementation departs from the plan, update the plan in the same commit.

## Stage 4, Code and Tests

Implement in small increments. Take the increment size from the agent context file. An increment that a human cannot review in a few minutes is too large.

Give yourself a feedback loop before you declare the work done. The loop is a test command, a build command, or a visual comparison, and it returns a clear pass or fail. Run the loop and correct the failures alone. End each task with proof that a person can see, run, or measure.

For a bug, write the failing test first. Run the test and confirm that it fails for the expected reason. Then make the test pass without a change to the test.

## Stage 5, Pull Request

The pull request is the review gate. Open a pull request and never push to the main branch. Never force-push, never bypass a hook, and never approve your own change.

Each implementation phase is its own stacked pull request. Branch phase N from phase N-1, and set the base branch of PR N to the branch of PR N-1. Each PR then carries only that phase. Merge from the bottom of the stack up. When a lower PR changes, restack the PRs above it and say so in the PR.

Review runs in two lanes:

- A reviewer agent performs the focused review. It reads every changed file, applies the repository rules, and reports structured findings with file and line, severity, evidence, and a suggested change. It reviews each PR against its own base. See [references/review-with-agents.md](references/review-with-agents.md).
- A human performs the light review. The human checks that the phase matches the intent, the spec, and the plan, then approves. Branch protection enforces the approval. The agent that wrote the code never approves it.

Answer review comments on your own pull requests. Fix the findings, or explain why a finding is wrong, and re-run the feedback loop.

## Working Rules

- Keep each change small and reviewable. Do not mix unrelated work.
- Implement only what the acceptance criteria require. Defer extra work unless the user asks for it.
- Do not use magic strings. A value used in more than one place becomes one named constant.
- Write the intent, the spec, and the plan in Simple English. Apply the `simple-english` skill.
- Update the agent context file, such as AGENTS.md or CLAUDE.md, when you repeat a mistake.

## Institutional Knowledge as Files

Read the working knowledge of the repository at the start of each session. The knowledge lives in versioned files.

- The agent context file holds the commands, the conventions, the architecture, the increment size, and the common mistakes. Keep it to about one page.
- The agent context file also names where the intent, the spec, the plan, and the ADRs live. Read those locations at the start of each session. If the locations are undocumented, ask before you choose.
- A skill holds a policy that must apply in a consistent way. The policy has one owner and one written source.
- A hook holds a rule that must always hold. The hook is deterministic, and it can allow an action, block an action, or ask for approval.

A skill increases the chance that the agent follows a rule. A hook makes a violation almost impossible. Use a hook for each rule with no exception.

## Hooks and the Production Gate

Act up to the production gate and never cross it. A hook enforces the gate.

A hook that asks for human approval suspends the action until the named person approves. The approval list comes from the change process, and the platform team translates each approval into a hook.

Run inside a sandbox with scoped credentials and no standing production credentials. A command that reaches production requires a release approval.

## Close the Loop

The loop closes when an event starts the agent with no person in the path. The event is a control threshold breach, a ticket, a channel message, or a schedule.

A deterministic script monitors the metric and detects a breach. The script holds the thresholds, and it does not use a model. When a threshold breaks, the script invokes the agent, and the agent diagnoses the cause and writes the findings as a new intent. The next stages then run in the normal way.

Keep the detection deterministic. Invoke the agent only after a threshold breaks, and set the allowed actions by tier.

## Measure the Result

Optimize for first-pass acceptance. The repository tracks a leading indicator and a lagging indicator per stage. See [references/metrics.md](references/metrics.md).

## Worked Example

See [references/worked-example.md](references/worked-example.md) for one repository's concrete layout and a stacked PR sequence.

## Integration

Use the `simple-english` skill for the intent, the spec, and the plan. Use the `design-interview` skill to resolve the decisions in the spec. Use the `premortem-this` skill to stress-test the spec before implementation. Use the `learn` skill when a decision depends on a concept the user does not know.
