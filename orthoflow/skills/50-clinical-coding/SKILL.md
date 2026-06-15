---
name: ortho-clinical-coding
description: Suggests ICD-10-GM + CHOP procedure codes and SwissDRG grouping from the case record and operative note, for a coder/surgeon to verify. Trigger when: coding suggestions are needed from documentation. Produces coding suggestions with the supporting documentation. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: admin
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# Clinical Coding

## Purpose

Suggests ICD-10-GM + CHOP procedure codes and SwissDRG grouping from the case record and operative note, for a coder/surgeon to verify. It suggests codes; a human verifies before any billing submission.

## When to use

Trigger this skill when:

- coding suggestions are needed from documentation.
- SwissDRG-relevant facts must be surfaced.

Do **not** use this skill when: to submit codes for billing without human verification.

## Workflow

### Step 1 — Extract codeable facts
Diagnosis, procedure, comorbidities, complications.

### Step 2 — Suggest codes
ICD-10-GM + CHOP; identify DRG-relevant factors.

### Step 3 — Flag gaps
Documentation that would not support a suggested code.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.suggest_coding({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
clinical_coding:
  coding_suggestions: <…>
  drg_relevant_factors: <…>
  documentation_gaps: <…>
  not_medical_advice: true
```

## Example

**Input:** Op note for an intertrochanteric fracture fixation with a diabetic patient.

**Skill response:** ICD-10-GM + CHOP suggestions; flags missing comorbidity documentation affecting DRG.

🩺 *Educational / decision-support reference only — not medical advice. A coder/surgeon verifies before submission.*

## Safety guardrails

- Suggestions only; a qualified coder/surgeon verifies before submission.
- No code is suggested that the documentation does not support.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-operative-note` — related step in the journey
- `ortho-billing-tariff` — related step in the journey
- `ortho-medical-necessity` — related step in the journey
