---
name: ortho-teaching-case
description: Converts a structured case into a resident teaching module with learning points and a reasoning walkthrough. Trigger when: a case should become a teaching module. Produces a teaching module with learning points. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: educational
x-domain: education
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Teaching Case

## Purpose

Converts a structured case into a resident teaching module with learning points and a reasoning walkthrough. It builds teaching material from anonymised cases; consent/anonymisation precede any use.

## When to use

Trigger this skill when:

- a case should become a teaching module.
- resident-facing educational content is needed from a real case.

Do **not** use this skill when: on identifiable patient data without anonymisation and consent.

## Workflow

### Step 1 — Anonymise
Confirm via governance.

### Step 2 — Structure
Case → reasoning → learning points.

### Step 3 — Add assessment hooks
Link to exam questions/competency.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.build_teaching_case({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
teaching_case:
  teaching_module: <…>
  learning_points: <…>
  reasoning_walkthrough: <…>
  not_medical_advice: true
```

## Example

**Input:** An instructive missed-classification case.

**Skill response:** Anonymised teaching module: the trap, the reasoning, the learning points.

🩺 *Educational / decision-support reference only — not medical advice. A teacher uses the module.*

## Safety guardrails

- Anonymisation + consent are preconditions.
- Teaches the reasoning, not just the answer.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-case-report-publishing` — related step in the journey
- `ortho-exam-question` — related step in the journey
- `ortho-skill-assessment` — related step in the journey
