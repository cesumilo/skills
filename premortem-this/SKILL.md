---
name: premortem-this
description: >
  Produces a premortem for a plan, project, or decision by imagining that it
  already failed and working backward to find the causes. Writes the full
  analysis alone and separates known facts from assumptions and speculation.
  The report leads with the most likely failure, the most dangerous failure,
  and the hidden assumption, then gives root-cause chains, early-warning
  signs, and ranked recommendations. Use when the user says "premortem this",
  "stress test this plan", "what could go wrong", "poke holes in this", or
  "what am I missing". Do not use when no concrete plan exists yet, when the
  decision is already irreversible, or when the user wants a critique of one
  decision's reasoning rather than a failure analysis.
---

# Premortem

Produce a premortem. Do not guide the user through discovery questions.
Gather the facts you need, then write the analysis and the report alone.

A premortem imagines that a plan already failed. Work backward from the
failure to find the causes. The method is prospective hindsight: the
"already failed" frame makes specific causes easier to name than the
question "what could go wrong?"

## Step 1, Gather the Minimum Context

Do not start the analysis until you can state three things:

1. What the plan, decision, or project is.
2. What success looks like.
3. The time horizon for the failure. The default is six months; for software, "two sprints" or "after launch" also work.

If the user already gave these facts, confirm them in one sentence. If facts
are missing, ask for all of them in one message. Do not run an
interrogation: infer what the conversation already implies.

Ask for these supporting facts when they are not obvious, but do not block
on them:

- The key stakeholders and systems.
- The constraints, such as budget, deadline, team size, and dependencies.
- The known risks and prior incidents.

If a fact is unknown, record it as unknown and continue.

## Step 2, Set the Scene

Frame the failure in one short paragraph. Name the time horizon and the
meaning of failure. For example:

> It is now [timeframe] later. The [project or decision] failed. The team did
> not meet the original goals, the work wasted resources, and stakeholder
> trust dropped. You are the investigator who writes the failure postmortem.

## Step 3, Find the Failure Causes

Write the analysis alone. Cover:

- Hidden assumptions: beliefs that turned out false about users, technology, the market, team capability, or constraints.
- Weak decisions: choices that looked reasonable and proved wrong, including each case where the team chose speed over safety.
- Missing or dismissed risks: risks the team saw and dismissed, risks never discussed, and foreseeable external shocks.
- Coordination and communication failures: misalignment that caused damage, and important information that arrived late or never.
- Most likely failure point: the single cause with the highest probability.
- Most dangerous failure point: the cause with the most severe consequences, even when less probable.

Tag every cause:

- `Known`: the plan or the context states it.
- `Assumed`: a plausible inference from the facts.
- `Speculative`: a possibility with no supporting fact.

Do not invent incidents, numbers, quotes, or decisions. If you cannot tag a
cause `Known` or `Assumed`, it is `Speculative`.

## Step 4, Trace Each Cause to a Root Cause

For each failure point, trace from symptom to root cause with the Five Whys:
start at "why did this happen?" and continue until you reach a systemic
cause. Do not stop at a surface answer and do not blame a person. Treat the
chain as plausible reasoning, not proof, and keep the `Known`, `Assumed`, or
`Speculative` tag on the finding.

Write each finding with the chain template in
[references/templates.md](references/templates.md). Stop when you reach a
root cause, even before five whys; continue past five when needed.

## Step 5, Write the Recommendations

For each root cause, write one or more recommendations. State the action,
the timing, the responsible role, the expected outcome, and the root cause
it addresses. Add one early-warning sign per recommendation: an observable
signal that shows the failure is starting.

Rank by expected harm: impact first, then likelihood. Rank 1 is the highest.

A root cause without a recommendation is a gap. Report each gap in the
report.

## Step 6, Produce the Report

Deliver one report, written in full without more input. Lead with a short
synthesis so a reader can act after 30 seconds:

- Most likely failure.
- Most dangerous failure.
- Hidden assumption.
- Top recommendation.

Then give the full sections: failure scenario, hidden assumptions, weak
decisions, missing risks, root-cause analysis, ranked recommendations, and
unaddressed root causes. The report template is in
[references/templates.md](references/templates.md). For a full example, see
[references/worked-example.md](references/worked-example.md).

Keep the report under about 1,200 words unless the user asks for more.

## Step 7, Offer the Variants

After the report, offer:

- A different time horizon, shorter or longer.
- A single dimension, such as technical failure, market adoption, or operations.
- A competitor premortem: imagine that a competitor won, then work backward.
- A decision premortem: run separate premortems for two options and compare them.

## Safety and Limits

The premortem is blame-free. Find systemic causes and do not assign fault.
Label every hypothesis and avoid catastrophizing language. Do not invent
facts to make a scenario feel real. State the limit once, in one sentence:
these are plausible causes to watch, not predictions. Close by noting that
confronting a worst case increases confidence in the plan, because the team
can correct the causes before they happen.

## Integration

Run this skill after a design-interview session to stress-test the clarified
plan, and before the ai-native-sdlc plan is approved. When the user needs to
understand a concept behind a finding, hand off to the `learn` skill.
