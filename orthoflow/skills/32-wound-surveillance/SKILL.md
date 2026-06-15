---
name: ortho-wound-surveillance
description: Reviews patient-submitted wound photos for escalation signals (SSI features) and routes concerning findings to the team. Trigger when: a patient submits a home wound photo for review. Produces a wound-concern status with escalation routing. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: homecare
x-archetype: classification
x-status: scaffold-v0.4-uncertified
---

# Wound Surveillance

## Purpose

Reviews patient-submitted wound photos for escalation signals (SSI features) and routes concerning findings to the team. It triages wound images for escalation; it does not diagnose infection.

## When to use

Trigger this skill when:

- a patient submits a home wound photo for review.
- surgical-site infection features must be screened.

Do **not** use this skill when: to confirm or exclude infection definitively — that needs clinical assessment.

## Workflow

### Step 1 — Check image adequacy
Is the photo adequate to comment on?

### Step 2 — Screen for SSI features
Erythema spread, discharge, dehiscence — descriptively.

### Step 3 — Route
Concerning → escalate to the team.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.surveil_wound({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
wound_surveillance:
  wound_status: <…>
  escalate: <…>
  image_adequate: <…>
  not_medical_advice: true
```

## Example

**Input:** Day-10 photo showing spreading erythema and discharge.

**Skill response:** wound_status=concerning; escalate=true; routed to the team.

🩺 *Educational / decision-support reference only — not medical advice. A clinician assesses and decides.*

## Safety guardrails

- Biased toward escalation on ambiguous wounds; a missed SSI is costly.
- Image-based triage only; never a definitive infection diagnosis.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-remote-monitoring` — related step in the journey
- `ortho-escalation-router` — related step in the journey
- `ortho-image-quality-check` — related step in the journey
