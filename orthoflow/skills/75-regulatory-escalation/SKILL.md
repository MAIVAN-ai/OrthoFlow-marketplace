---
name: ortho-regulatory-escalation
description: Flags when an event triggers MDR vigilance, serious-incident, or adverse-event reporting relevance, and routes it. Trigger when: an event may meet a vigilance/serious-incident reporting threshold. Produces a regulatory-relevance flag with routing. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: governance
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Regulatory Escalation

## Purpose

Flags when an event triggers MDR vigilance, serious-incident, or adverse-event reporting relevance, and routes it. It flags reporting relevance; the regulatory function decides and reports.

## When to use

Trigger this skill when:

- an event may meet a vigilance/serious-incident reporting threshold.
- an adverse event linked to a device/skill must be assessed for reporting.

Do **not** use this skill when: to make or imply a regulatory determination.

## Workflow

### Step 1 — Assess event
Against MDR vigilance / serious-incident criteria.

### Step 2 — Flag relevance
With the criterion matched.

### Step 3 — Route
To the regulatory/quality function.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.flag_regulatory({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
regulatory_escalation:
  regulatory_flag: <…>
  criterion: <…>
  routing: <…>
  not_medical_advice: true
```

## Example

**Input:** A possible device-related complication pattern.

**Skill response:** Flags possible vigilance relevance; routes to the regulatory function for determination.

🩺 *Educational / decision-support reference only — not medical advice. The regulatory function decides and reports.*

## Safety guardrails

- Flags relevance only; never asserts a reporting obligation is or isn't met.
- Biased toward flagging when uncertain (under-reporting is the costly direction).
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-pmcf-capture` — related step in the journey
- `ortho-clinical-responsibility` — related step in the journey
- `ortho-data-governance-gate` — related step in the journey
