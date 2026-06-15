---
name: ortho-preop-optimisation
description: Screens modifiable pre-operative risk — anaemia, diabetes, smoking, nutrition, frailty, anticoagulation, infection risk — and assembles an optimisation checklist for clinician review. Trigger when: an elective case is being prepared and modifiable risk should be addressed before surgery. Produces an optimisation checklist mapped to the case. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: periop
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Pre-operative Optimisation

## Purpose

Screens modifiable pre-operative risk — anaemia, diabetes, smoking, nutrition, frailty, anticoagulation, infection risk — and assembles an optimisation checklist for clinician review. It surfaces an optimisation checklist; it does not prescribe.

## When to use

Trigger this skill when:

- an elective case is being prepared and modifiable risk should be addressed before surgery.
- ERAS-style pre-operative optimisation is needed.

Do **not** use this skill when: an emergency case where optimisation would delay necessary surgery.

## Workflow

### Step 1 — Screen modifiable factors
Anaemia, glycaemic control, smoking, nutrition, frailty, infection sources.

### Step 2 — Assemble checklist
With evidence-linked rationale, for clinician review.

### Step 3 — Flag book/hold
Whether the case is optimisation-ready to book.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.preop_optimise({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
preop_optimisation:
  optimisation_checklist: <…>
  book_readiness: <…>
  flagged_factors: <…>
  not_medical_advice: true
```

## Example

**Input:** Elective TKA, Hb 9.8, HbA1c 9.1%, active smoker.

**Skill response:** Checklist: anaemia work-up, glycaemic optimisation, smoking cessation referral; book_readiness=hold.

🩺 *Educational / decision-support reference only — not medical advice. The surgical/anaesthetic team decides on optimisation and timing.*

## Safety guardrails

- Surfaces options for clinician decision; never issues medication or dosing instructions (that is prescribing tier).
- Defers to the anaesthetic/medical team on perioperative medication management.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-preop-risk-stratification` — related step in the journey
- `ortho-vte-prophylaxis-planning` — related step in the journey
- `ortho-treatment-mapping` — related step in the journey
