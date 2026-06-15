---
name: ortho-cost-approval
description: Drafts insurer pre-authorisation (Kostengutsprache) requests with the clinical justification pre-assembled, for clinician sign-off. Trigger when: an insurer pre-authorisation is required for an elective procedure. Produces a Kostengutsprache draft with justification. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: admin
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Cost Approval (Kostengutsprache)

## Purpose

Drafts insurer pre-authorisation (Kostengutsprache) requests with the clinical justification pre-assembled, for clinician sign-off. It drafts the request; the clinician signs and submits.

## When to use

Trigger this skill when:

- an insurer pre-authorisation is required for an elective procedure.
- a Kostengutsprache request must be assembled.

Do **not** use this skill when: to submit a request without clinician sign-off.

## Workflow

### Step 1 — Assemble justification
Indication, evidence, alternatives tried.

### Step 2 — Draft request
In insurer-ready form.

### Step 3 — Present for sign-off
Clinician signs.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.draft_cost_approval({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
cost_approval:
  cost_approval_draft: <…>
  clinical_justification: <…>
  payer: <…>
  not_medical_advice: true
```

## Example

**Input:** Elective ACL reconstruction needing pre-authorisation.

**Skill response:** Drafted request with indication + failed-conservative-management evidence; presented for sign-off.

🩺 *Educational / decision-support reference only — not medical advice. The clinician signs and submits.*

## Safety guardrails

- Draft only; clinician signs and submits.
- Justification is grounded in the record; nothing is asserted that is unsupported.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-medical-necessity` — related step in the journey
- `ortho-billing-tariff` — related step in the journey
- `ortho-treatment-mapping` — related step in the journey
