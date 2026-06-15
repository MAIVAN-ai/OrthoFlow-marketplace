---
name: ortho-intraop-guidance
description: Provides educational intra-operative reference support — reduction adequacy, implant position, fluoroscopy view adequacy — from images the surgeon provides. Trigger when: a surgeon requests a reference read on an intra-operative image (reduction, implant position, view adequacy). Produces a reference assessment with confidence, for the surgeon to weigh. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: periop
x-archetype: classification
x-status: scaffold-v0.4-uncertified
---

# Intra-operative Guidance

## Purpose

Provides educational intra-operative reference support — reduction adequacy, implant position, fluoroscopy view adequacy — from images the surgeon provides. It is reference/decision-support that informs the surgeon; it does NOT control robots or actuate anything. OrthoFlow plans and informs; it never executes surgical action.

## When to use

Trigger this skill when:

- a surgeon requests a reference read on an intra-operative image (reduction, implant position, view adequacy).
- an intra-op fluoroscopy view needs an adequacy check.

Do **not** use this skill when: to drive, command, or close the loop on any device or robotic system.

## Workflow

### Step 1 — Check image adequacy
Is the intra-op view adequate to comment on?

### Step 2 — Assess reduction/position
Alignment, length, rotation, implant position — descriptively, with confidence.

### Step 3 — Surface for the surgeon
Present as reference, never as a command.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.intraop_reference({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
intraop_guidance:
  assessment: <…>
  confidence: <…>
  view_adequate: <…>
  not_medical_advice: true
```

## Example

**Input:** Intra-op fluoro of a cephalomedullary nail lag screw.

**Skill response:** Reference: tip-apex distance appears within typical range; confidence reported; surgeon decides.

🩺 *Educational / decision-support reference only — not medical advice. The operating surgeon decides and acts.*

## Safety guardrails

- Strictly informational; never actuates or commands a device (keeps OrthoFlow below the surgical-execution boundary).
- Defers to the operating surgeon's direct view; low-confidence reads say so.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-image-quality-check` — related step in the journey
- `ortho-implant-selection` — related step in the journey
- `ortho-operative-note` — related step in the journey
