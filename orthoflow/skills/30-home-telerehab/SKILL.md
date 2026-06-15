---
name: ortho-home-telerehab
description: Generates a procedure-specific home rehabilitation plan with weight-bearing limits and red-flag stop conditions, for the treating team's approval. Trigger when: a patient is at home and needs a procedure-specific exercise/weight-bearing plan. Produces a home rehab plan with explicit red-flag limits. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: homecare
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Home Tele-rehab

## Purpose

Generates a procedure-specific home rehabilitation plan with weight-bearing limits and red-flag stop conditions, for the treating team's approval. It delivers the at-home rehab protocol; the surgeon's specific protocol takes precedence.

## When to use

Trigger this skill when:

- a patient is at home and needs a procedure-specific exercise/weight-bearing plan.
- tele-rehab delivery is required between clinic visits.

Do **not** use this skill when: to override the treating surgeon's prescribed protocol.

## Workflow

### Step 1 — Map procedure to protocol
Weight-bearing stage, ROM milestones, precautions.

### Step 2 — Personalise
To the patient's progress and constraints.

### Step 3 — Embed stop-rules
Red flags that escalate back to the surgeon.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.home_telerehab({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
home_telerehab:
  rehab_plan: <…>
  weight_bearing_stage: <…>
  red_flag_limits: <…>
  not_medical_advice: true
```

## Example

**Input:** 6 weeks post fixed ankle fracture, cleared for progressive weight-bearing.

**Skill response:** Staged home plan with stop-rules (increasing pain/swelling → escalate).

🩺 *Educational / decision-support reference only — not medical advice. The treating surgeon's protocol governs.*

## Safety guardrails

- Always subordinate to the treating surgeon's specific post-op protocol.
- Red-flag limits route to ortho-escalation-router, never 'push through'.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-aftercare-rehab` — related step in the journey
- `ortho-remote-monitoring` — related step in the journey
- `ortho-patient-instructions` — related step in the journey
