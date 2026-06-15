---
name: ortho-preop-risk-stratification
description: Applies predictive analytics to estimate surgical risk (ASA-adjacent, frailty, cardiac, transfusion) to support — not replace — the clinical risk assessment. Trigger when: a case needs structured risk stratification to support shared decision-making and planning. Produces calibrated risk bands with the factors driving them. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: periop
x-archetype: classification
x-status: scaffold-v0.4-uncertified
---

# Pre-operative Risk Stratification

## Purpose

Applies predictive analytics to estimate surgical risk (ASA-adjacent, frailty, cardiac, transfusion) to support — not replace — the clinical risk assessment. It estimates risk bands with calibrated uncertainty; the clinician owns the assessment. It is the Risk-Stratification agent in the perioperative ensemble.

## When to use

Trigger this skill when:

- a case needs structured risk stratification to support shared decision-making and planning.
- frailty/transfusion/readmission risk should be made explicit.

Do **not** use this skill when: as a substitute for formal anaesthetic assessment.

## Workflow

### Step 1 — Gather risk inputs
Comorbidities, frailty, labs, prior outcomes from the normalised record.

### Step 2 — Estimate risk bands
With explicit confidence/calibration.

### Step 3 — Explain drivers
Surface the factors driving the estimate for clinician scrutiny.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.stratify_risk({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
preop_risk_stratification:
  risk_bands: <…>
  key_drivers: <…>
  confidence: <…>
  not_medical_advice: true
```

## Example

**Input:** 82M hip fracture, frailty, anticoagulated.

**Skill response:** risk_bands={mortality:elevated, transfusion:high}; drivers named; confidence reported.

🩺 *Educational / decision-support reference only — not medical advice. The clinical team owns the risk assessment.*

## Safety guardrails

- Reports calibrated uncertainty; an over-confident risk score is a certification failure.
- Names the drivers (explainability) so a clinician can challenge the estimate.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-preop-optimisation` — related step in the journey
- `ortho-complication-risk-prediction` — related step in the journey
- `ortho-shared-decision-support` — related step in the journey
