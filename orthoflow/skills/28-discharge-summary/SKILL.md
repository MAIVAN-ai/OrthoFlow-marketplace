---
name: ortho-discharge-summary
description: Generates a structured GP-facing discharge summary auto-populated from the case record for clinician sign-off. Trigger when: a discharge letter is needed at the end of an inpatient episode. Produces a discharge-summary draft. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: periop
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Discharge Summary

## Purpose

Generates a structured GP-facing discharge summary auto-populated from the case record for clinician sign-off. It drafts; the clinician signs.

## When to use

Trigger this skill when:

- a discharge letter is needed at the end of an inpatient episode.
- a structured GP-facing summary should be auto-populated.

Do **not** use this skill when: to send a discharge letter without sign-off.

## Workflow

### Step 1 — Assemble episode
Diagnosis, procedure, course, medications, follow-up, weight-bearing.

### Step 2 — Draft summary
GP-facing, structured, with medication reconciliation flagged.

### Step 3 — Present for sign-off
Clinician edits and signs.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.draft_discharge({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
discharge_summary:
  discharge_summary_draft: <…>
  followup_plan: <…>
  medication_reconciliation_flag: <…>
  not_medical_advice: true
```

## Example

**Input:** Post hip-fracture fixation, ready for discharge to rehab.

**Skill response:** Draft GP letter; flags anticoagulation restart decision for clinician confirmation.

🩺 *Educational / decision-support reference only — not medical advice. The discharging clinician signs the letter.*

## Safety guardrails

- Medication changes (esp. anticoagulation restart) are flagged for explicit clinician confirmation.
- Draft only; never auto-sent.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-operative-note` — related step in the journey
- `ortho-aftercare-rehab` — related step in the journey
- `ortho-patient-instructions` — related step in the journey
