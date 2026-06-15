---
name: ortho-pmcf-capture
description: Maps each case's implant + classification + outcome into manufacturer-facing post-market clinical follow-up evidence (MDR Art. 61 / Annex XIV). Trigger when: a case can contribute to an implant's post-market clinical follow-up evidence. Produces a PMCF evidence packet with provenance. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: outcomes
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# PMCF Evidence Capture

## Purpose

Maps each case's implant + classification + outcome into manufacturer-facing post-market clinical follow-up evidence (MDR Art. 61 / Annex XIV). It packages real-world evidence for PMCF; it makes no regulatory determination. This is the engine of PMCF-as-a-Service and the production-side drift signal for model certification.

## When to use

Trigger this skill when:

- a case can contribute to an implant's post-market clinical follow-up evidence.
- PMCF evidence packets must be generated from real-world cases.

Do **not** use this skill when: to make or imply a regulatory conformity decision.

## Workflow

### Step 1 — Link implant–classification–outcome
From the case record and outcomes.

### Step 2 — De-identify+govern
Via ortho-data-governance-gate; check secondary-use consent.

### Step 3 — Package
Manufacturer-facing PMCF summary with provenance.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.capture_pmcf({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
pmcf_capture:
  pmcf_packet: <…>
  implant_outcome_linkage: <…>
  governance_attestation: <…>
  not_medical_advice: true
```

## Example

**Input:** A cohort of one implant class with 1-year outcomes and consent for secondary use.

**Skill response:** De-identified PMCF packet with linkage and governance attestation.

🩺 *Educational / decision-support reference only — not medical advice. Regulatory/quality governance owns the determination.*

## Safety guardrails

- No identifiable data leaves; de-identification + consent are preconditions, enforced by the governance gate.
- Provides evidence, not a regulatory conclusion.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-implant-outcome-link` — related step in the journey
- `ortho-data-governance-gate` — related step in the journey
- `ortho-regulatory-escalation` — related step in the journey
