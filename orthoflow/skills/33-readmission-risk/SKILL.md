---
name: ortho-readmission-risk
description: Estimates risk of ED visit/readmission from symptoms and pathway deviation, with the action that would mitigate it. Trigger when: a discharged patient's readmission risk should be made explicit. Produces a readmission-risk band with a mitigating action. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: homecare
x-archetype: classification
x-status: scaffold-v0.4-uncertified
---

# Readmission Risk

## Purpose

Estimates risk of ED visit/readmission from symptoms and pathway deviation, with the action that would mitigate it. It estimates risk and suggests touchpoints; it does not decide admission.

## When to use

Trigger this skill when:

- a discharged patient's readmission risk should be made explicit.
- pathway deviation suggests rising risk.

Do **not** use this skill when: as an admission/no-admission decision.

## Workflow

### Step 1 — Assemble signals
Symptoms, PROMs, pathway adherence, social factors.

### Step 2 — Estimate risk
With calibration and drivers.

### Step 3 — Suggest mitigation
Touchpoint, escalation, or reassurance.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.predict_readmission({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
readmission_risk:
  readmission_risk: <…>
  drivers: <…>
  suggested_action: <…>
  not_medical_advice: true
```

## Example

**Input:** Poor pain control + missed physio + lives alone.

**Skill response:** Elevated risk band; suggests proactive nurse call.

🩺 *Educational / decision-support reference only — not medical advice. The clinical team acts on the risk.*

## Safety guardrails

- Calibrated; drivers named for clinician scrutiny.
- Advisory only; admission decisions rest with clinicians.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-remote-monitoring` — related step in the journey
- `ortho-pathway-adherence` — related step in the journey
- `ortho-complication-risk-prediction` — related step in the journey
