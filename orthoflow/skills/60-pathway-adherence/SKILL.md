---
name: ortho-pathway-adherence
description: Checks whether care followed the agreed clinical pathway and scores adherence with the deviations made explicit. Trigger when: pathway adherence must be measured for qa. Produces an adherence score with deviations. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: quality
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Pathway Adherence

## Purpose

Checks whether care followed the agreed clinical pathway and scores adherence with the deviations made explicit. It measures adherence; it does not judge clinical appropriateness of justified deviations.

## When to use

Trigger this skill when:

- pathway adherence must be measured for QA.
- deviations from an agreed pathway need surfacing.

Do **not** use this skill when: to penalise clinically justified deviations.

## Workflow

### Step 1 — Compare to pathway
Care record vs agreed pathway.

### Step 2 — Score adherence
With deviation list.

### Step 3 — Contextualise
Flag justified vs unexplained deviations.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.check_adherence({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
pathway_adherence:
  adherence_score: <…>
  deviations: <…>
  unexplained_deviations: <…>
  not_medical_advice: true
```

## Example

**Input:** A hip-fracture pathway with delayed time-to-surgery.

**Skill response:** Adherence score with the delay flagged and its documented reason surfaced.

🩺 *Educational / decision-support reference only — not medical advice. QA governance interprets the score.*

## Safety guardrails

- Distinguishes justified clinical deviation from process failure; does not penalise the former.
- Outputs feed QA, never individual punitive action without human review.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-hospital-qa-benchmark` — related step in the journey
- `ortho-mm-review` — related step in the journey
- `ortho-readmission-risk` — related step in the journey
