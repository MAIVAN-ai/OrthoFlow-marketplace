---
name: ortho-mm-review
description: Structures morbidity & mortality / complication case preparation with a root-cause skeleton for the review meeting. Trigger when: an m&m or complication review must be prepared. Produces a structured M&M case brief. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: quality
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# M&M Review

## Purpose

Structures morbidity & mortality / complication case preparation with a root-cause skeleton for the review meeting. It structures the review material; the meeting reaches conclusions.

## When to use

Trigger this skill when:

- an M&M or complication review must be prepared.
- a structured root-cause skeleton is needed.

Do **not** use this skill when: to assign blame or reach the review's conclusions.

## Workflow

### Step 1 — Assemble case
Timeline, decisions, outcome.

### Step 2 — Structure root-cause
Contributing-factor skeleton (no conclusions).

### Step 3 — Prepare brief
For the meeting.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.prepare_mm({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
mm_review:
  mm_brief: <…>
  contributing_factors_skeleton: <…>
  timeline: <…>
  not_medical_advice: true
```

## Example

**Input:** An unexpected return to theatre.

**Skill response:** Structured brief with a contributing-factor skeleton for the M&M meeting.

🩺 *Educational / decision-support reference only — not medical advice. The M&M meeting reaches conclusions.*

## Safety guardrails

- Presents factors for discussion; never asserts causation or blame.
- Blinded/anonymised as the QA process requires.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-pathway-adherence` — related step in the journey
- `ortho-hospital-qa-benchmark` — related step in the journey
- `ortho-case-report-publishing` — related step in the journey
