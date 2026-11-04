---
name: premortem-this
description: >
  Produces a premortem for a plan, project, or decision by imagining that it
  already failed and working backward to find the causes. The agent asks for the
  information it needs, then writes the full analysis alone. The report covers
  hidden assumptions, weak decisions, missing risks, root causes, and ranked
  recommendations. Activate it when the user says "premortem this", "stress test
  this plan", "what could go wrong", "poke holes in this", or "what am I missing".
---

# premortem-this

This skill makes the agent produce a premortem. The agent does not guide the
user through discovery questions. The agent gathers the facts it needs, then
writes the analysis and the report alone.

A premortem imagines that a plan already failed. The agent works backward from
the failure to find the causes. The method uses prospective hindsight.

## When to Use

- Before a product launch, a feature release, or a service launch.
- At the start of a complex project that spans several sprints.
- Before a major architectural or technical decision.
- Before a high-stakes event, such as a migration, a launch, or a pitch.
- When the user chooses between options with a severe downside.
- After a design-interview session, to stress-test the clarified plan.

## Step 1, Gather the Required Information

The agent must have enough facts before it starts the premortem. If the user
gave the facts already, the agent confirms them. If the user gave some facts,
the agent asks for the missing ones.

The agent asks for these items:

1. The plan, decision, or project under review, with its goal and its success criteria.
2. The key stakeholders and the systems involved.
3. The time horizon for the failure. The default is six months. For software, the agent uses "two sprints" or "after launch".
4. The constraints, such as budget, deadline, team size, and dependencies.
5. The known risks and the prior incidents.

The agent asks all questions in one message. The agent does not begin the
analysis until the user answers or confirms that no more facts exist. If a fact
is unknown, the agent records it as unknown and continues.

## Step 2, Set the Scene

The agent frames the failure in one short paragraph. The frame names the time
horizon and the meaning of failure. For example:

> It is now [timeframe] later. The [project or decision] failed. The team did
> not meet the original goals, the work wasted resources, and the trust of the
> stakeholders dropped. You are the investigator who writes the failure
> postmortem.

## Step 3, Find the Failure Causes

The agent writes the analysis alone. The agent covers these areas:

- Hidden assumptions. The beliefs that turned out to be false, about users, technology, the market, the capabilities of the team, or the constraints.
- Weak decisions. The decision points that looked reasonable at the time and proved wrong. Include each case where the team chose speed over safety.
- Missing or dismissed risks. The risks that the team saw and dismissed, the risks that the team never discussed, and the foreseeable external shocks.
- Coordination and communication failures. The places where misalignment between people or teams caused damage. Include the important information that did not reach the right people in time.
- Most likely failure point. The single cause with the highest probability.
- Most dangerous failure point. The cause with the most severe consequences, even when the probability is lower.

## Step 4, Trace Each Cause to a Root Cause

For each failure point, the agent traces from the symptom to the root cause. The
agent uses the Five Whys method. The agent starts with "why did this happen?"
and drills deeper until it reaches a systemic cause. The agent does not stop at
a surface answer and does not blame a person.

The agent writes each finding in this form:

```
Finding: [symptom]

Why chain
1. Why? [immediate cause]
2. Why? [deeper cause]
3. Why? [even deeper cause]
4. Why? [systemic factor]
5. Why? [root cause, a structural gap in process, incentives, knowledge, tooling, or organization]

Root cause: [one sentence with the deepest systemic factor]
```

If the agent reaches a root cause before five whys, it stops. If the agent needs
more than five whys, it continues.

## Step 5, Write the Recommendations

For each root cause, the agent writes one or more recommendations. Each
recommendation states five things: the action, the timing, the responsible role,
the expected outcome, and the root cause it addresses. The agent ranks each
recommendation by impact. Rank 1 has the highest impact and the most urgency.

The agent writes each recommendation in this form:

```
Recommendation 1 (Impact: high)

Action: [concrete action]
Timing: [before launch, during sprint planning, or continuous]
Role: [role, not a person, for example tech lead, product owner, or operations]
Expected outcome: [what changes]
Addresses: [root causes]
```

A root cause without a recommendation is a gap. The agent reports each such gap
in the final report.

## Step 6, Produce the Report

The agent delivers one report. The report is the only artifact of the skill, and
the agent writes it in full without more input from the user. The report has
these sections:

```
Premortem report: [plan or decision]

Failure scenario
[the frame, with the timeframe and the meaning of failure]

Hidden assumptions
[list]

Weak decisions
[list, with the reason each decision looked right and the reason it was wrong]

Missing risks
[list, with the reason each risk was overlooked]

Most likely failure point
[the single most probable cause]

Most dangerous failure point
[the most severe cause, even when the probability is lower]

Root cause analysis
[each finding with its why chain and its root cause]

Recommendations, ranked by impact
[each recommendation in the form above]

Unaddressed root causes
[each root cause without a matching recommendation]
```

## Step 7, Offer the Variants

After the report, the agent offers these options:

- A different time horizon, for example a shorter horizon or a longer horizon.
- A single dimension, for example technical failure, market adoption, or operations.
- A competitor premortem, which imagines that a competitor won and works backward.
- A decision premortem, which runs separate premortems for two options and compares them.

## Safety and Mindset

The premortem is a blame-free analysis. The agent finds systemic causes and does
not assign fault. Each hypothesis is a possibility, not a prediction, so the
agent does not increase the fear of the user. After the analysis, the agent
states one fact: confronting a worst-case scenario increases confidence in the
plan, because the team can correct the causes before they happen.

## Integration

The agent runs this skill after a design-interview session, because the
interview clarifies the plan and this skill stress-tests it. If the session
produces artifacts, the agent writes them with the chunked method from the
file-writing skill. When the user needs to understand a concept behind a finding,
the agent hands off to the learn skill.
