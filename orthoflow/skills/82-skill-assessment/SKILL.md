---
name: ortho-skill-assessment
description: Assesses a resident's reasoning against an expert pathway and produces a competency report. Trigger when: a resident's reasoning should be compared to an expert pathway. Produces a competency report with gaps and feedback. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: educational
x-domain: education
x-archetype: classification
x-status: scaffold-v0.4-uncertified
---

# Skill Assessment

## Purpose

Assesses a resident's reasoning against an expert pathway and produces a competency report. It gives structured formative feedback; faculty own summative judgments.

## When to use

Trigger this skill when:

- a resident's reasoning should be compared to an expert pathway.
- a competency/feedback report is wanted.

Do **not** use this skill when: for summative/high-stakes decisions without faculty oversight.

## Workflow

### Step 1 — Capture reasoning
The resident's pathway.

### Step 2 — Compare
Against the expert reference pathway.

### Step 3 — Report
Gaps + constructive feedback.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.assess_skill({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
skill_assessment:
  competency_report: <…>
  gaps: <…>
  feedback: <…>
  not_medical_advice: true
```

## Example

**Input:** A trainee's classification + treatment reasoning on a case.

**Skill response:** Competency report: strong classification, gap in differential breadth, with feedback.

🩺 *Educational / decision-support reference only — not medical advice. Faculty own summative assessment.*

## Safety guardrails

- Formative by default; faculty own summative use.
- Feedback is constructive, never punitive.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-teaching-case` — related step in the journey
- `ortho-exam-question` — related step in the journey
- `ortho-differential-reasoning` — related step in the journey
