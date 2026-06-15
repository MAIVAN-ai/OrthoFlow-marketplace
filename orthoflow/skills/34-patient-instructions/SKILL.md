---
name: ortho-patient-instructions
description: Produces patient-facing, health-literacy-adapted, multilingual (DE/FR/IT) instructions from clinician content. Trigger when: clinician content must be turned into patient-friendly instructions. Produces patient-facing instructions in the chosen language and reading level. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: homecare
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Patient Instructions

## Purpose

Produces patient-facing, health-literacy-adapted, multilingual (DE/FR/IT) instructions from clinician content. It translates clinician-approved content into accessible language; it adds no new clinical advice.

## When to use

Trigger this skill when:

- clinician content must be turned into patient-friendly instructions.
- multilingual (DE/FR/IT) patient material is needed.

Do **not** use this skill when: to generate clinical advice not present in the approved source.

## Workflow

### Step 1 — Take approved content
From the clinician/discharge summary.

### Step 2 — Adapt
To reading level and language; preserve clinical meaning.

### Step 3 — Flag ambiguities
Anything needing clinician clarification.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.patient_instructions({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
patient_instructions:
  patient_instructions: <…>
  language: <…>
  reading_level: <…>
  not_medical_advice: true
```

## Example

**Input:** Discharge weight-bearing instructions, patient prefers Italian, low health literacy.

**Skill response:** Plain-language Italian instructions; flags one ambiguous medication line for clinician.

🩺 *Educational / decision-support reference only — not medical advice. The clinician approves the source content.*

## Safety guardrails

- Adds no clinical content beyond the approved source; preserves meaning exactly.
- Flags rather than guesses when source content is ambiguous.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-discharge-summary` — related step in the journey
- `ortho-home-telerehab` — related step in the journey
- `ortho-shared-decision-support` — related step in the journey
