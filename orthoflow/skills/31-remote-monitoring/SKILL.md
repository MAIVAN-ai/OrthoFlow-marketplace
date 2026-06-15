---
name: ortho-remote-monitoring
description: Continuously analyses wearable and PROM data to detect concerning trends early and coordinate intervention before complications manifest. Trigger when: home wearable/prom data should be watched for deterioration. Produces a trend status with any early-warning flag. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: homecare
x-archetype: ranking_differential
x-status: scaffold-v0.4-uncertified
---

# Remote Monitoring

## Purpose

Continuously analyses wearable and PROM data to detect concerning trends early and coordinate intervention before complications manifest. It surfaces early-warning signals; clinicians intervene.

## When to use

Trigger this skill when:

- home wearable/PROM data should be watched for deterioration.
- an early-warning signal needs surfacing to the care team.

Do **not** use this skill when: as a substitute for an in-person assessment when one is indicated.

## Workflow

### Step 1 — Ingest home data
Wearables, home PROMs, patient check-ins (internal only).

### Step 2 — Detect trend
Normal vs concerning trajectory vs pathway deviation.

### Step 3 — Coordinate
Flag concerning trends to the team early.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.remote_monitor({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
remote_monitoring:
  trend_status: <…>
  early_warning: <…>
  recommended_touchpoint: <…>
  not_medical_advice: true
```

## Example

**Input:** Rising pain PROM + falling step count two weeks post-op.

**Skill response:** early_warning=possible complication; recommends clinician touchpoint.

🩺 *Educational / decision-support reference only — not medical advice. The care team decides on intervention.*

## Safety guardrails

- Tuned to surface deterioration early; missed deterioration is the costly direction.
- Does not autonomously change care; it flags for clinician action.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-home-telerehab` — related step in the journey
- `ortho-wound-surveillance` — related step in the journey
- `ortho-readmission-risk` — related step in the journey
