---
name: ortho-bias-fairness
description: Checks for pathway bias across age, sex, geography and socioeconomic status — AND for access equity, the risk that the technology concentrates in well-resourced settings and widens disparities. Trigger when: outputs or pathways must be checked for demographic bias. Produces a bias + access-equity report. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: governance
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Bias & Fairness

## Purpose

Checks for pathway bias across age, sex, geography and socioeconomic status — AND for access equity, the risk that the technology concentrates in well-resourced settings and widens disparities. It surfaces bias and equity risks; humans remediate. Access equity is the RRR/rural dimension the literature highlights.

## When to use

Trigger this skill when:

- outputs or pathways must be checked for demographic bias.
- access-equity (who benefits) must be assessed.

Do **not** use this skill when: as a one-off; bias/equity monitoring is continuous.

## Workflow

### Step 1 — Slice performance
By age, sex, geography, socioeconomic status.

### Step 2 — Assess access equity
Is the benefit concentrating in well-resourced settings?

### Step 3 — Report
Disparities and remediation options.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.check_bias({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
bias_fairness:
  bias_report: <…>
  access_equity_assessment: <…>
  remediation_options: <…>
  not_medical_advice: true
```

## Example

**Input:** A classification skill validated only on urban tertiary data.

**Skill response:** Flags rural/RRR under-validation as an access-equity risk; recommends slice expansion.

🩺 *Educational / decision-support reference only — not medical advice. Governance owns remediation.*

## Safety guardrails

- Checks both output bias and access equity; a model strong on tertiary-centre data is flagged for rural under-validation.
- Feeds the certification harness's per-slice gates (incl. the rrr_distribution slice).
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-hospital-qa-benchmark` — related step in the journey
- `ortho-clinical-responsibility` — related step in the journey
- `ortho-data-governance-gate` — related step in the journey
