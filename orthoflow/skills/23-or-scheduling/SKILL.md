---
name: ortho-or-scheduling
description: Estimates case duration/complexity and supports OR booking against urgency, fasting, implant lead-time and team availability — including real-time re-sequencing in high-acuity trauma. Trigger when: a case must be booked or an or list re-sequenced. Produces a booking/re-sequencing recommendation with the trade-offs. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: periop
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# OR Scheduling

## Purpose

Estimates case duration/complexity and supports OR booking against urgency, fasting, implant lead-time and team availability — including real-time re-sequencing in high-acuity trauma. It proposes schedule options; theatre management approves. In trauma it supports hierarchical, real-time re-prioritisation of emergent cases.

## When to use

Trigger this skill when:

- a case must be booked or an OR list re-sequenced.
- trauma demands real-time re-prioritisation and resource redistribution.

Do **not** use this skill when: to make the final binding booking without human approval.

## Workflow

### Step 1 — Estimate duration/complexity
From procedure, anatomy, revision status, comorbidities.

### Step 2 — Optimise the list
Against urgency, fasting, implant lead-time, team/equipment availability.

### Step 3 — Re-sequence on change
In trauma, re-prioritise emergent cases and flag displaced ones.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.schedule_or({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
or_scheduling:
  booking_recommendation: <…>
  resequence_actions: <…>
  tradeoffs: <…>
  not_medical_advice: true
```

## Example

**Input:** Trauma list disrupted by an incoming open fracture.

**Skill response:** Re-sequences list, flags the elective case displaced, surfaces the trade-off for the coordinator.

🩺 *Educational / decision-support reference only — not medical advice. Theatre management approves the schedule.*

## Safety guardrails

- Emergent/urgent clinical priority always overrides efficiency optimisation.
- Displaced cases are surfaced explicitly so no patient silently drops off the list.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-referral-triage` — related step in the journey
- `ortho-journey-orchestrator` — related step in the journey
- `ortho-preop-risk-stratification` — related step in the journey
