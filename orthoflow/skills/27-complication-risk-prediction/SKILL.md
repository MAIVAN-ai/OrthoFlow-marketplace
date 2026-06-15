---
name: ortho-complication-risk-prediction
description: Forward-looking prediction of implant success and complication risk from multimodal data (radiomics, wearables, outcomes) to support perioperative and outpatient decisions. Trigger when: a personalised complication/implant-success prediction would support a decision. Produces a calibrated risk prediction with drivers and confidence. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: periop
x-archetype: classification
x-status: scaffold-v0.4-uncertified
---

# Complication Risk Prediction

## Purpose

Forward-looking prediction of implant success and complication risk from multimodal data (radiomics, wearables, outcomes) to support perioperative and outpatient decisions. It predicts with calibrated uncertainty; it does not decide. Distinct from retrospective outcome measurement.

## When to use

Trigger this skill when:

- a personalised complication/implant-success prediction would support a decision.
- multimodal data (imaging+PROMs+wearables) can inform forward risk.

Do **not** use this skill when: as a deterministic claim about an individual's outcome.

## Workflow

### Step 1 — Assemble multimodal features
Radiomics, PROMs, wearable trends, comorbidities.

### Step 2 — Predict risk
With explicit calibration and confidence interval.

### Step 3 — Explain
Surface drivers; route high-risk to clinician review.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.predict_complication_risk({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
complication_risk_prediction:
  predicted_risk: <…>
  confidence_interval: <…>
  drivers: <…>
  not_medical_advice: true
```

## Example

**Input:** Cemented hemiarthroplasty, frailty, low activity wearable trend.

**Skill response:** Elevated dislocation-risk band with CI and drivers; routed to clinician for shared decision-making.

🩺 *Educational / decision-support reference only — not medical advice. The clinician weighs the prediction in context.*

## Safety guardrails

- Calibration is mandatory; over-confident predictions fail certification.
- Predictions are probabilistic support, never a guarantee or an autonomous decision.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-preop-risk-stratification` — related step in the journey
- `ortho-remote-monitoring` — related step in the journey
- `ortho-outcome-measurement` — related step in the journey
