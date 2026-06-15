---
name: ortho-data-governance-gate
description: Decides, per field, what may flow to the ORTHO-X Data Commons vs what stays locked — consent-for-secondary-use, de-identification, Swiss Trust Layer attestation, and the 'authorities only if legally compelled' carve-out. Trigger when: any data is about to flow beyond direct care (commons, pmcf, registry, analytics). Produces a per-field permit/deny decision with attestation. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: governance
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Data Governance Gate

## Purpose

Decides, per field, what may flow to the ORTHO-X Data Commons vs what stays locked — consent-for-secondary-use, de-identification, Swiss Trust Layer attestation, and the 'authorities only if legally compelled' carve-out. It is the technical enforcement of the data-sovereignty promise. It defaults to deny.

## When to use

Trigger this skill when:

- any data is about to flow beyond direct care (commons, PMCF, registry, analytics).
- a disclosure request (incl. from authorities) must be evaluated.

Do **not** use this skill when: to permit any identifiable export without consent and attestation.

## Workflow

### Step 1 — Classify data
Identifiable vs de-identified vs aggregate.

### Step 2 — Check basis
Consent for secondary use; legal basis; 'authorities only if compelled'.

### Step 3 — Decide+attest
Permit/deny per field; Swiss Trust Layer attestation on permits.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.govern_data({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
data_governance_gate:
  per_field_decision: <…>
  consent_basis: <…>
  attestation: <…>
  not_medical_advice: true
```

## Example

**Input:** A PMCF request including a near-identifiable rare-case field.

**Skill response:** Permits de-identified fields; denies the re-identifying field; attests the permitted flow.

🩺 *Educational / decision-support reference only — not medical advice. Data governance owns the policy; the gate enforces it.*

## Safety guardrails

- Default-deny: ambiguous or unconsented data is withheld. False-permit is the catastrophic direction and a hard certification veto.
- Never auto-permits identifiable export to MedTech; authorities only where legally compelled (Art. 321 StGB / FADP).
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-consent-management` — related step in the journey
- `ortho-pmcf-capture` — related step in the journey
- `ortho-regulatory-escalation` — related step in the journey
