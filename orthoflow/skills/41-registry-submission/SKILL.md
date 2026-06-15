---
name: ortho-registry-submission
description: Prepares and validates national/implant registry exports with code validation and eligibility/consent checks. Trigger when: a case is eligible for registry submission and an export must be prepared. Produces a validated registry export package. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: outcomes
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# Registry Submission

## Purpose

Prepares and validates national/implant registry exports with code validation and eligibility/consent checks. It prepares a validated export; a human authorises submission.

## When to use

Trigger this skill when:

- a case is eligible for registry submission and an export must be prepared.
- registry codes need validation before submission.

Do **not** use this skill when: to submit without consent and human authorisation.

## Workflow

### Step 1 — Check eligibility+consent
Including ortho-consent-management.

### Step 2 — Assemble+validate
Map and validate registry codes.

### Step 3 — Present for authorisation
Human authorises submission.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.prepare_registry({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
registry_submission:
  registry_export: <…>
  validation_result: <…>
  consent_status: <…>
  not_medical_advice: true
```

## Example

**Input:** Primary THA eligible for the national arthroplasty registry.

**Skill response:** Validated export; consent confirmed; presented for authorisation.

🩺 *Educational / decision-support reference only — not medical advice. A human authorises the submission.*

## Safety guardrails

- No submission without confirmed consent and human authorisation.
- De-identification per ortho-data-governance-gate before any external flow.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-prom-collection` — related step in the journey
- `ortho-data-governance-gate` — related step in the journey
- `ortho-pmcf-capture` — related step in the journey
