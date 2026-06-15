---
name: ortho-escalation-router
description: The 'break out and get a human now' skill — defines the hard stops where autonomy must hand back to a human and routes to the right one. Trigger when: any skill raises a red flag, low confidence, or an out-of-bounds situation. Produces an escalation decision and the human to route to. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: meta
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Escalation Router

## Purpose

The 'break out and get a human now' skill — defines the hard stops where autonomy must hand back to a human and routes to the right one. It is the safety backstop across the whole system; it always errs toward escalation.

## When to use

Trigger this skill when:

- any skill raises a red flag, low confidence, or an out-of-bounds situation.
- a hard stop requires a human now.

Do **not** use this skill when: never suppressed — it is the system's safety valve.

## Workflow

### Step 1 — Detect trigger
Red flag, low confidence, out-of-certified-bounds, conflict.

### Step 2 — Choose recipient
The appropriate human/role.

### Step 3 — Hand off + record
Escalate and log via traceability.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.route_escalation({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
escalation_router:
  escalate: <…>
  recipient: <…>
  trigger: <…>
  not_medical_advice: true
```

## Example

**Input:** A classification below the certified confidence bound.

**Skill response:** escalate=true; recipient=on-call surgeon; trigger=out-of-bounds; logged.

🩺 *Educational / decision-support reference only — not medical advice. A human takes over on escalation.*

## Safety guardrails

- Always errs toward escalation; suppressing it is impossible by design.
- Out-of-certified-bounds operation auto-escalates (ties to the autonomy gate).
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-red-flag-screen` — related step in the journey
- `ortho-journey-orchestrator` — related step in the journey
- `ortho-clinical-responsibility` — related step in the journey
