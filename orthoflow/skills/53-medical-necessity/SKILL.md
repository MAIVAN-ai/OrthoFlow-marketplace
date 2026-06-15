---
name: ortho-medical-necessity
description: Structures the medical-necessity argument for a payer (and appeals for denied claims), grounded in guidelines and the record. Trigger when: a payer requires a structured medical-necessity justification. Produces a structured medical-necessity / appeal draft. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: admin
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Medical Necessity

## Purpose

Structures the medical-necessity argument for a payer (and appeals for denied claims), grounded in guidelines and the record. It structures the argument; the clinician owns the clinical claim.

## When to use

Trigger this skill when:

- a payer requires a structured medical-necessity justification.
- a denied claim needs a grounded appeal.

Do **not** use this skill when: to assert necessity unsupported by the record or guidelines.

## Workflow

### Step 1 — Map indication to guideline
Appropriateness/guideline grounding.

### Step 2 — Structure necessity
Or the appeal to a denial.

### Step 3 — Present for sign-off
Clinician reviews.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.structure_necessity({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
medical_necessity:
  necessity_argument: <…>
  guideline_basis: <…>
  appeal_draft: <…>
  not_medical_advice: true
```

## Example

**Input:** Denied claim for revision arthroplasty.

**Skill response:** Structured appeal citing guideline indication and failed alternatives; for clinician sign-off.

🩺 *Educational / decision-support reference only — not medical advice. The clinician signs the argument.*

## Safety guardrails

- Grounded in guidelines + record; no overstated claims.
- Clinician owns and signs the clinical argument.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-cost-approval` — related step in the journey
- `ortho-evidence-retrieval` — related step in the journey
- `ortho-treatment-mapping` — related step in the journey
