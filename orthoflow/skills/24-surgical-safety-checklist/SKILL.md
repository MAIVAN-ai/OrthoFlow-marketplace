---
name: ortho-surgical-safety-checklist
description: Supports the WHO surgical safety checklist and time-out — site/side/implant/allergy verification — at the point of care. Trigger when: a who-style time-out / safety check is being performed. Produces a verification result with any mismatch flagged. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: periop
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Surgical Safety Checklist

## Purpose

Supports the WHO surgical safety checklist and time-out — site/side/implant/allergy verification — at the point of care. It supports verification; the team performs and owns the time-out. Wrong-site surgery is a never-event.

## When to use

Trigger this skill when:

- a WHO-style time-out / safety check is being performed.
- site, side, implant or allergy must be verified against the record.

Do **not** use this skill when: as a replacement for the team's verbal time-out.

## Workflow

### Step 1 — Pull verification facts
Site, side, procedure, implant, allergies from the verified record.

### Step 2 — Cross-check
Against consent and booking; flag any discrepancy loudly.

### Step 3 — Confirm completion
Record the human-performed time-out.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.safety_checklist({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
surgical_safety_checklist:
  verification_result: <…>
  discrepancies: <…>
  timeout_recorded: <…>
  not_medical_advice: true
```

## Example

**Input:** Booked left TKA; consent says right.

**Skill response:** discrepancy=laterality_mismatch; hard stop surfaced to the team before incision.

🩺 *Educational / decision-support reference only — not medical advice. The surgical team performs and owns the time-out.*

## Safety guardrails

- Any site/side/implant/allergy discrepancy is a hard stop surfaced to the whole team.
- Never auto-confirms; the team confirms verbally.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-operative-note` — related step in the journey
- `ortho-implant-selection` — related step in the journey
- `ortho-clinical-responsibility` — related step in the journey
