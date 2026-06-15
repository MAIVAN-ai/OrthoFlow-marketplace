---
name: ortho-implant-outcome-link
description: Links implant type/class to outcomes without exposing patient identity, producing an implant-outcome data point for the commons. Trigger when: an implant must be linked to its outcome as a de-identified data point. Produces a de-identified implant-outcome data point. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: outcomes
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# Implant–Outcome Link

## Purpose

Links implant type/class to outcomes without exposing patient identity, producing an implant-outcome data point for the commons. It creates de-identified linkage points; it does not benchmark or rank products.

## When to use

Trigger this skill when:

- an implant must be linked to its outcome as a de-identified data point.
- the data commons needs implant-outcome signal.

Do **not** use this skill when: to produce identifiable or product-defamatory claims.

## Workflow

### Step 1 — Resolve implant+outcome
From record and PROMs.

### Step 2 — De-identify
Strip identifiers; check re-identification risk.

### Step 3 — Emit data point
For the governed commons.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.link_implant_outcome({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
implant_outcome_link:
  implant_outcome_point: <…>
  deidentification_status: <…>
  not_medical_advice: true
```

## Example

**Input:** Implant class X with a revision event at 18 months.

**Skill response:** De-identified linkage point; re-identification risk passed; emitted to commons.

🩺 *Educational / decision-support reference only — not medical advice. Data governance owns the commons.*

## Safety guardrails

- Re-identification risk is checked, not assumed; high-risk points are withheld.
- Governed by ortho-data-governance-gate and consent.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-pmcf-capture` — related step in the journey
- `ortho-data-governance-gate` — related step in the journey
- `ortho-outcome-measurement` — related step in the journey
