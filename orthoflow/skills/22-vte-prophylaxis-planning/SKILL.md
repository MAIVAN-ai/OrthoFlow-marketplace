---
name: ortho-vte-prophylaxis-planning
description: Drafts VTE prophylaxis considerations (agent class, duration windows, mechanical options) for clinician review after major orthopaedic surgery. Trigger when: a post-operative vte prophylaxis plan needs drafting for clinician review. Produces a draft prophylaxis consideration set (never an executable order). Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: prescribing
x-domain: periop
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# VTE Prophylaxis Planning

## Purpose

Drafts VTE prophylaxis considerations (agent class, duration windows, mechanical options) for clinician review after major orthopaedic surgery. It drafts considerations for sign-off; it never prescribes a drug or dose. Orthopaedics is the highest-VTE-risk surgery, so the guardrails are heaviest here.

## When to use

Trigger this skill when:

- a post-operative VTE prophylaxis plan needs drafting for clinician review.
- prophylaxis duration/adherence should be made explicit.

Do **not** use this skill when: to issue a prescription, dose, or to override a clinician's chosen regimen.

## Workflow

### Step 1 — Assess VTE/bleeding balance
From procedure, mobility, comorbidities, bleeding risk.

### Step 2 — Surface considerations
Agent classes, mechanical options, typical duration windows — as a draft.

### Step 3 — Route for sign-off
Present to the responsible clinician; do not finalise.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.draft_vte_prophylaxis({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
vte_prophylaxis_planning:
  prophylaxis_considerations_draft: <…>
  vte_risk: <…>
  bleeding_risk: <…>
  not_medical_advice: true
```

## Example

**Input:** Post total hip replacement, standard bleeding risk.

**Skill response:** Draft: pharmacological + mechanical considerations, extended-duration window flagged — presented for clinician sign-off.

🩺 *Educational / decision-support reference only — not medical advice. A qualified clinician prescribes and signs.*

## Safety guardrails

- Never emits a drug name+dose as an order; surfaces classes and considerations only.
- Defers entirely to the treating clinician and local protocol.
- **Human-in-the-loop (mandatory).** This is a `prescribing`-tier skill: a qualified clinician must confirm every output; the skill refuses to emit an executable order and surfaces a draft for sign-off.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-preop-optimisation` — related step in the journey
- `ortho-aftercare-rehab` — related step in the journey
- `ortho-clinical-responsibility` — related step in the journey
