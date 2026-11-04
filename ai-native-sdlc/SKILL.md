---
name: ai-native-sdlc
description: Teaches the agent to work with the artifact chain. Every change moves through intent, spec, plan, code with tests, and a pull request. Each stage commits a versioned artifact that the next stage reads, and the commit history is the audit trail. Activate this skill for feature work, refactors, and bug fixes in a repository that uses this method.
---

# AI-native SDLC, the Artifact Chain

This skill defines the working method for a repository that follows the AI-native software development life cycle (SDLC). The method replaces long handoffs between teams with a chain of versioned artifacts. The agent produces each artifact, and a human reviews it at the checkpoint.

## The Core Rule

Every change moves through five stages in order. Each stage commits one artifact, and the next stage starts by reading that artifact.

1. Intent: the problem, the outcome, and the constraints.
2. Spec: the requirements and the design.
3. Plan: the files, the order of work, and the proof.
4. Code and tests: the implementation and its checks.
5. Pull request: the review and the approval.

The commit history is the audit trail. It records who asked for the change, what the agent produced, and who approved it.

## Stage 1, Intent

The intent is the source of truth for the change. In most repositories the issue tracker holds the intent, and the agent reads it first.

The intent states five things: the problem, the proposed outcome, the affected users and systems, the constraints, and the open questions.

If the user gives an idea in conversation, the agent writes the intent to a file or an issue before any other work. The agent asks for the missing facts and does not guess.

## Stage 2, Spec

The spec holds the requirements and the design in one document. The agent produces the spec from the intent in one session.

The agent applies the policies of the organization while it writes the spec. The policies come from skills, such as brand, security, compliance, and user experience (UX). The agent flags each area of concern, especially a conflict between two policies.

A human product owner reviews the spec against the intent. The spec must answer each open question from the intent or carry the question forward.

## Stage 3, Plan

No implementation starts without an approved plan. The agent writes the plan before it changes any code.

The plan states the files that change, the order of work, the risks, and the proof of success. The plan must be complete enough that a different engineer can do the work from the plan alone.

The agent tests the plan before approval. It asks what the change can break, which step is the riskiest, and which options it rejected.

When the implementation departs from the plan, the agent updates the plan in the same commit.

## Stage 4, Code and Tests

The agent implements in small increments. In this repository an increment stays under about 500 added lines, so a human can review it before the next increment.

The agent gives itself a feedback loop before it declares the work done. The loop is a test command, a build command, or a visual comparison, and it returns a clear pass or fail. The agent runs the loop and corrects the failures alone.

For a bug, the agent writes the failing test first. The agent runs the test and confirms that it fails for the expected reason. The agent then makes the test pass without a change to the test.

## Stage 5, Pull Request

The pull request is the review gate. The agent opens a pull request and never pushes to the main branch.

The agent reviews in both directions. It reviews incoming pull requests against the repository rules, and it answers the review comments on its own pull requests.

The review policy lives in a file, such as REVIEW.md. The policy ranks each finding by severity, and it separates an important finding from a minor one. A human approves the merge through branch protection. The agent that wrote the code cannot approve it.

## Working Rules

These rules apply at every stage.

- Keep each change small and reviewable. Do not mix unrelated work.
- Implement only what the acceptance criteria require. Defer extra work unless the user asks for it.
- End each task with proof that a person can see, run, or measure.
- Do not use magic strings. A value used in more than one place becomes one named constant.
- Write the intent, the spec, and the plan in Simple English. Apply the simple-english skill.
- Propose a commit title after each change set. Do not commit unless the user asks.
- Update the agent context file, such as AGENTS.md or CLAUDE.md, when the agent repeats a mistake.

## Institutional Knowledge as Files

The agent reads the working knowledge of the repository at the start of each session. The knowledge lives in versioned files.

- The agent context file, such as AGENTS.md or CLAUDE.md, holds the commands, the conventions, the architecture, and the common mistakes. Keep it to about one page.
- A skill holds a policy that must apply in a consistent way. The policy has one owner and one written source.
- A hook holds a rule that must always hold. The hook is deterministic, and it can allow an action, block an action, or ask for approval.

A skill increases the chance that the agent follows a rule. A hook makes a violation almost impossible. Use a hook for each rule with no exception.

## Hooks and the Production Gate

The agent acts up to the production gate and never crosses it. A hook enforces the gate.

A hook that asks for human approval suspends the action until the named person approves. The approval list comes from the change process, and the platform team translates each approval into a hook.

The agent runs inside a sandbox with scoped credentials and no standing production credentials. A command that reaches production requires a release approval.

## Close the Loop

The loop closes when an event starts the agent with no person in the path. The event is a control threshold breach, a ticket, a channel message, or a schedule.

A deterministic script monitors the metric and detects a breach. The script holds the thresholds, and it does not use a model. When a threshold breaks, the script invokes the agent, and the agent diagnoses the cause and writes the findings as a new intent. The next stages then run in the normal way.

Keep the detection deterministic. Invoke the agent only after a threshold breaks, and set the allowed actions by tier.

## Measure the Result

Each stage has a leading indicator and a lagging indicator. The leading indicator shows the speed of the stage, and the lagging indicator shows the quality.

- Intent: the time from the first conversation to a committed intent, and the share of intents that the product owner accepts.
- Spec: the time from the intent commit to the spec commit, and the requirements rework after the build starts.
- Plan: the share of changes that merge from the first implementation pass, and the rework cycles per change.
- Code: the share of failures that the loop catches without a person, and the defects that escape to production.
- Pull request: the time to the first review, and the defects that escape to production.

## Worked Example

The rove repository applies this method. The intent is a Gitea issue with the sections Goal, Acceptance, and Feedback contract. The spec and the plan live in `docs/specs/<number>-<slug>/`, and the team commits them before the code. The hard decisions live in `docs/adr/` as Architecture Decision Records (ADRs). The agent context file is `AGENTS.md`, and it names the artifact chain, the increment size, and the commands that prove the work.

## Integration

Use the simple-english skill for the intent, the spec, and the plan. Use the design-interview skill to resolve the decisions in the spec. Use the premortem-this skill to stress-test the spec before implementation. Use the file-writing skill for each artifact that the agent writes. Use the learn skill when a decision depends on a concept that the user does not know.
