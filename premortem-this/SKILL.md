---
name: premortem-this
description: >
  Stress-tests any plan, project, or decision by imagining it has already failed
  and working backward to discover why. Surfaces hidden assumptions, weak
  decisions, and missing risks before resources are committed. Activate when the
  user says "premortem this", "stress test this plan", "what could go wrong",
  "poke holes in this", "what am I missing", or after a design-interview session
  to stress-test a clarified plan.
---

# premortem-this

## Description
Stress-tests any plan, project, or decision by imagining it has already failed and working backward to discover why. Uses prospective hindsight (popularized by Gary Klein) to surface hidden assumptions, weak decisions, missing risks, and the most likely and most dangerous failure points — before resources are committed.

## When to Use
- Launching a new product, feature, or service
- Starting a complex multi-sprint project
- Making a major architectural or technical decision
- Preparing for high-stakes events (migrations, launches, pitches)
- Choosing between multiple options where the downside is severe
- After using **design-interview** to clarify a plan — premortem stress-tests that clarified plan

## Instructions

You are facilitating a premortem. Follow this structure.

### 1. Gather Context
If the user hasn't already provided it, ask for:
- The plan, decision, or project being evaluated
- Key stakeholders or systems involved
- Time horizon for the failure (default: 6 months; adjust for software: "2 sprints" or "after launch")

### 2. Set the Scene
Vividly frame the failure scenario:
> "It is now [timeframe] later. The [project/decision] has failed badly. The original goals were not met. Resources were wasted. Trust was damaged. You are the investigator writing the failure postmortem. Your job is to explain exactly what happened and why."

### 3. Guide Discovery
Use open-ended, blame-safe questions. Draw out:

**Hidden Assumptions**
- What did we believe to be true that turned out to be false?
- What did we assume about users, technology, the market, our capabilities, or our constraints?

**Weak Decisions**
- Which specific decision points seemed reasonable at the time but proved wrong?
- Where did we prioritise speed over safety in a way that backfired?

**Missing or Dismissed Risks**
- What risks did we see but dismiss as unlikely or unimportant?
- What risks did we never even discuss?
- What external shock hit us that was foreseeable?

**Coordination and Communication Failures**
- Where did misalignment between people or teams cause damage?
- What important information didn't reach the right people in time?

**Most Likely Failure Point**
- If you had to bet on a single cause, which one would it be?

**Most Dangerous Failure Point**
- Which cause would have the most severe consequences, even if it's less likely?

### 4. Root Cause Analysis
For every failure point surfaced in discovery, trace from symptom to root cause. Use the Five Whys technique: starting from "why did this happen?", drill deeper until you reach a systemic or structural cause. Avoid stopping at surface-level answers or blaming individuals. For each finding, record:

```
## Finding: [Symptom]

**Why Chain**
1. Why? → [immediate cause]
2. Why? → [deeper cause]
3. Why? → [even deeper cause]
4. Why? → [systemic factor]
5. Why? → [root cause — a structural gap in process, incentives, knowledge, tooling, or organisational design]

**Root Cause (summary)**: [One sentence that captures the deepest systemic factor]
```

If you hit a root cause earlier than 5 whys, stop — no need to force it. If more than 5 whys are needed, continue.

### 5. Generate Recommendations
For every root cause, produce one or more concrete, ranked recommendations. Each recommendation must:
- Describe **what** action to take
- Specify **when** it should be done (before launch, during sprint planning, continuously, etc.)
- Name **who** is responsible (role, not person; e.g. "tech lead", "product owner", "ops")
- State the **expected outcome** (what changes if this is done)
- Be **ranked** by impact (1 = highest impact / most urgent)

Root causes without a corresponding recommendation are a red flag — every root cause deserves at least one mitigation.

```
### Recommendation 1 (Impact: ⭐⭐⭐)

**What**: [Concrete action]
**When**: [Timing]
**Who**: [Role responsible]
**Expected outcome**: [What this prevents or enables]
**Traces to**: [Root cause(s) this addresses]
```

### 6. Synthesise and Report
Deliver a structured summary:

```
## Premortem Report: [Plan/Decision Name]

### Failure Scenario
[Restate the frame: timeframe, what "failed" means]

### Hidden Assumptions
- [Assumption 1]
- [Assumption 2]

### Weak Decisions
- [Decision 1] — why it seemed right, why it was wrong
- [Decision 2]

### Missing Risks
- [Risk 1] — why it was overlooked
- [Risk 2]

### Most Likely Failure Point
[Single most probable cause — the bet-you'd-make answer]

### Most Dangerous Failure Point
[Most severe consequence cause, even if lower probability]

### Root Cause Analysis
For every finding above, trace from symptom to root cause using Five Whys.

**[Finding A]**
1. Why? → ...
2. Why? → ...
3. Why? → ...
**Root Cause**: [systemic factor]

**[Finding B]**
1. Why? → ...
...

### Recommendations (Ranked by Impact)
Each recommendation traces back to a root cause above.

**Recommendation 1 (Impact: ⭐⭐⭐)**
- What: [Concrete action]
- When: [Timing]
- Who: [Role]
- Expected outcome: [What changes]
- Traces to: [Root cause(s)]

**Recommendation 2 (Impact: ⭐⭐)**
...

### Unaddressed Root Causes
[Call out any root cause that lacks a matching recommendation — this is a gap]
```

### 7. Offer Customisation
After the report, offer these variants:
- **Adjust timeframe** — "Want to run this again with a shorter/longer horizon?"
- **Focus on a dimension** — "Want to zero in on just technical failure / market adoption / operations?"
- **Competitor premortem** — "Want to imagine our competitor beat us and work backward from that?"
- **Decision premortem** — If the user is choosing between options, offer: "Want to run separate premortems for Option A and Option B and compare?"

## Safety and Mindset
- This is a **blame-free zone**. You are finding systemic causes, not assigning fault.
- All hypotheses are possibilities, not predictions. Avoid reinforcing catastrophising.
- After the exercise, explicitly note that confronting worst-case scenarios builds *more* confidence in the plan, not less.
- Follow the reasoning transparency standards from the **problem-solving** skill: make your thinking visible, avoid silent internal loops, and flag when you change approach.

## Integration with Other Skills
- **design-interview**: Run after design-interview has clarified the plan. Design-interview sharpens; premortem stress-tests.
- **long-term-memory**: Prompt the user to record premortem findings as architectural decisions or open questions in MEMORY.md.
- **ddd**: Use premortem to stress-test bounded context boundaries and aggregate designs before implementation.
- **problem-solving**: Adhere to its hypothesis-driven, iterate-fast reasoning style throughout.

