---
name: ortho-patient-history-summarisation
description: Summarises a longitudinal patient history from the journey memory layer for clinician review — a core copilot function. Trigger when: a clinician needs a faithful longitudinal summary of a patient's orthopaedic history. Produces a faithful longitudinal history summary with provenance. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: meta
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Patient History Summarisation

## Purpose

Summarises a longitudinal patient history from the journey memory layer for clinician review — a core copilot function. It summarises faithfully from the record; it adds no new clinical content. It is the surface of the longitudinal-memory layer.

## When to use

Trigger this skill when:

- a clinician needs a faithful longitudinal summary of a patient's orthopaedic history.
- prior episodes must be condensed for a current decision.

Do **not** use this skill when: to infer history not present in the record.

## Workflow

### Step 1 — Pull longitudinal record
From the memory layer (internal only).

### Step 2 — Summarise faithfully
Key episodes, implants, outcomes — with provenance.

### Step 3 — Flag gaps
Where the record is incomplete.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.summarise_history({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
patient_history_summarisation:
  history_summary: <…>
  provenance: <…>
  record_gaps: <…>
  not_medical_advice: true
```

## Example

**Input:** A revision-arthroplasty patient with a decade of records.

**Skill response:** A faithful, provenance-linked summary; flags a missing prior-op-note gap.

🩺 *Educational / decision-support reference only — not medical advice. The clinician reviews the summary.*

## Safety guardrails

- Adds no clinical content beyond the record; every line is traceable.
- Flags incompleteness rather than smoothing over gaps.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-ehr-normalisation` — related step in the journey
- `ortho-case-intake` — related step in the journey
- `ortho-journey-orchestrator` — related step in the journey
