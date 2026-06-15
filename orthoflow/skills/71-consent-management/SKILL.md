---
name: ortho-consent-management
description: Tracks and audits the consent chain — including consent for the agent to LEARN from the case (model training / secondary use / federated learning), not only care and research. Trigger when: a case's consent scope must be established or audited. Produces a consent matrix with the learn/secondary-use status explicit. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: governance
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Consent Management

## Purpose

Tracks and audits the consent chain — including consent for the agent to LEARN from the case (model training / secondary use / federated learning), not only care and research. It manages the consent matrix; it does not obtain consent. The 'consent-to-learn' dimension is explicit, per the literature's concern about continuously-learning agents.

## When to use

Trigger this skill when:

- a case's consent scope must be established or audited.
- secondary-use / model-training consent must be checked before any data flow.

Do **not** use this skill when: to assume consent that is not recorded.

## Workflow

### Step 1 — Map consent scope
Care / second-opinion / registry / PMCF / research / model-training.

### Step 2 — Audit the chain
Who consented to what, when.

### Step 3 — Gate downstream
Block flows lacking the relevant consent.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.manage_consent({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
consent_management:
  consent_matrix: <…>
  learn_consent: <…>
  audit_log: <…>
  not_medical_advice: true
```

## Example

**Input:** A case consented for care and registry but not for model training.

**Skill response:** Matrix shows learn_consent=false; blocks any training/commons flow.

🩺 *Educational / decision-support reference only — not medical advice. The clinician obtains consent; the gate records it.*

## Safety guardrails

- Absence of recorded consent is treated as no-consent, never as implied consent.
- Consent-to-learn is tracked distinctly from consent-to-treat and consent-to-research.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-data-governance-gate` — related step in the journey
- `ortho-case-completeness` — related step in the journey
- `ortho-prom-collection` — related step in the journey
