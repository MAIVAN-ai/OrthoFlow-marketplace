---
name: ortho-prom-collection
description: Collects and structures validated PROMs (HOOS, KOOS, Oxford Hip/Knee, EQ-5D, PROMIS) into a clean dataset linked to the case. Trigger when: proms must be captured at a defined timepoint. Produces a structured, timepoint-linked PROM dataset. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: outcomes
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# PROM Collection

## Purpose

Collects and structures validated PROMs (HOOS, KOOS, Oxford Hip/Knee, EQ-5D, PROMIS) into a clean dataset linked to the case. It captures and structures PROMs; it does not interpret them clinically.

## When to use

Trigger this skill when:

- PROMs must be captured at a defined timepoint.
- structured outcome data is needed for outcomes/PMCF/registry.

Do **not** use this skill when: to alter or impute patient-reported responses.

## Workflow

### Step 1 — Administer/ingest
The appropriate validated instrument for the joint/procedure.

### Step 2 — Structure
Score and link to case + timepoint.

### Step 3 — Validate
Flag incomplete or inconsistent responses.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.collect_prom({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
prom_collection:
  prom_dataset: <…>
  instrument: <…>
  timepoint: <…>
  not_medical_advice: true
```

## Example

**Input:** 12-month Oxford Knee Score for a TKA patient.

**Skill response:** Structured OKS linked to case+timepoint; one missing item flagged.

🩺 *Educational / decision-support reference only — not medical advice. Clinical/research governance owns interpretation.*

## Safety guardrails

- Never imputes or edits patient responses; missing items are flagged.
- Consent for secondary use is checked before any flow to the commons (ortho-consent-management).
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-outcome-measurement` — related step in the journey
- `ortho-implant-outcome-link` — related step in the journey
- `ortho-pmcf-capture` — related step in the journey
