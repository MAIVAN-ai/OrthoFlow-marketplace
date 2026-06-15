---
name: ortho-billing-tariff
description: Maps outpatient services to the Swiss tariff under the TARMED→TARDOC transition and cross-checks against the liability pathway. Trigger when: outpatient services must be mapped to tardoc/ambulatory flat-rates. Produces draft tariff positions with cross-checks. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: admin
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# Billing & Tariff

## Purpose

Maps outpatient services to the Swiss tariff under the TARMED→TARDOC transition and cross-checks against the liability pathway. It drafts tariff positions for review; it does not bill.

## When to use

Trigger this skill when:

- outpatient services must be mapped to TARDOC/ambulatory flat-rates.
- tariff and liability pathway must be cross-checked.

Do **not** use this skill when: to submit a bill without human review.

## Workflow

### Step 1 — Map services
To current tariff positions (TARDOC transition aware).

### Step 2 — Cross-check liability
KVG/UVG/IV pathway consistency.

### Step 3 — Flag issues
Mismatches for human review.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.draft_tariff({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
billing_tariff:
  tariff_positions: <…>
  liability_pathway: <…>
  flags: <…>
  not_medical_advice: true
```

## Example

**Input:** Outpatient fracture follow-up after a workplace accident.

**Skill response:** Draft TARDOC positions; flags UVG (accident) liability for confirmation.

🩺 *Educational / decision-support reference only — not medical advice. Billing/revenue staff review and submit.*

## Safety guardrails

- Draft positions only; human reviews before billing.
- Tariff rules change; positions are checked against the current tariff, not assumed.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-clinical-coding` — related step in the journey
- `ortho-cost-approval` — related step in the journey
- `ortho-medical-necessity` — related step in the journey
