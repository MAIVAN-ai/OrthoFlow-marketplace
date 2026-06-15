---
name: ortho-red-flag-screen
description: Screens for orthopaedic emergencies and do-not-miss conditions — infection, neurovascular compromise, cauda equina, compartment syndrome, open fracture, malignancy, implant failure. Trigger when: any new case is being assessed and emergencies must be excluded before routine reasoning. Produces an escalate-now decision with the reason, or a cleared-to-proceed flag. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: core
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Red Flag Screen

## Purpose

Screens for orthopaedic emergencies and do-not-miss conditions — infection, neurovascular compromise, cauda equina, compartment syndrome, open fracture, malignancy, implant failure. It raises and escalates alarms; it does not manage the emergency.

## When to use

Trigger this skill when:

- any new case is being assessed and emergencies must be excluded before routine reasoning.
- a referral or intake mentions a possible do-not-miss condition.

Do **not** use this skill when: never skip it — red-flag screening runs on every new case.

## Workflow

### Step 1 — Screen do-not-miss list
Infection, neurovascular, cauda equina, compartment syndrome, open fracture, malignancy mimic, implant failure.

### Step 2 — Decide
escalate-now (with reason) or cleared-to-proceed.

### Step 3 — Route
On escalate, hand to ortho-escalation-router immediately.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.screen_red_flags({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
red_flag_screen:
  red_flag_detected: <…>
  condition: <…>
  escalate_now: <…>
  reason: <…>
  not_medical_advice: true
```

## Example

**Input:** Post-op calf pain, tense compartment, pain on passive stretch.

**Skill response:** red_flag=compartment_syndrome; escalate_now=true; routed to ortho-escalation-router.

🩺 *Educational / decision-support reference only — not medical advice. A clinician acts on any escalation immediately.*

## Safety guardrails

- Maximal sensitivity by design: a missed red flag is the catastrophic failure; ambiguity escalates.
- Sensitivity on the do-not-miss set is a hard certification veto (see eval harness).
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-referral-triage` — related step in the journey
- `ortho-escalation-router` — related step in the journey
- `ortho-differential-reasoning` — related step in the journey
