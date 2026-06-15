---
name: ortho-study-design-advisor
description: Recommends methodology, sample size and measurement approaches for biomechanical studies, trials or basic-science experiments given a research question. Trigger when: a study design needs methodology/sample-size/measurement advice. Produces design recommendations with rationale. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: educational
x-domain: research
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Study Design Advisor

## Purpose

Recommends methodology, sample size and measurement approaches for biomechanical studies, trials or basic-science experiments given a research question. It advises on design; the investigator decides and a statistician confirms power.

## When to use

Trigger this skill when:

- a study design needs methodology/sample-size/measurement advice.
- measurement approaches must be matched to a research question.

Do **not** use this skill when: as a substitute for formal statistical/methodological sign-off.

## Workflow

### Step 1 — Clarify the question
Outcome, comparison, constraints.

### Step 2 — Recommend design
Methodology + measurement approach.

### Step 3 — Estimate sample size
With assumptions stated, for statistician confirmation.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.advise_study_design({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
study_design_advisor:
  design_recommendation: <…>
  sample_size_estimate: <…>
  assumptions: <…>
  not_medical_advice: true
```

## Example

**Input:** A question comparing two fixation constructs biomechanically.

**Skill response:** Recommends a design + measurement + a sample-size estimate with explicit assumptions.

🩺 *Educational / decision-support reference only — not medical advice. A statistician confirms power.*

## Safety guardrails

- Sample-size estimates state their assumptions and require statistician confirmation.
- Advisory; the investigator owns the design.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-research-hypothesis` — related step in the journey
- `ortho-literature-synthesis` — related step in the journey
- `ortho-outcome-measurement` — related step in the journey
