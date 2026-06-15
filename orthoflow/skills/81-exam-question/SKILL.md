---
name: ortho-exam-question
description: Generates board-style questions with explanations from real (anonymised) cases. Trigger when: board-style practice questions are needed. Produces MCQs with explanations. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: educational
x-domain: education
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Exam Question

## Purpose

Generates board-style questions with explanations from real (anonymised) cases. It produces practice items; faculty validate them.

## When to use

Trigger this skill when:

- board-style practice questions are needed.
- a case can seed assessment items.

Do **not** use this skill when: to produce items presented as officially validated without faculty review.

## Workflow

### Step 1 — Derive stem
From an anonymised case.

### Step 2 — Build options
Plausible distractors + correct answer.

### Step 3 — Explain
Rationale for each option.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.generate_exam_question({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
exam_question:
  mcqs: <…>
  explanations: <…>
  difficulty: <…>
  not_medical_advice: true
```

## Example

**Input:** A Schatzker tibial plateau case.

**Skill response:** A board-style MCQ with explained distractors.

🩺 *Educational / decision-support reference only — not medical advice. Faculty validate the items.*

## Safety guardrails

- Faculty validate before any high-stakes use.
- Anonymisation precedes generation.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-teaching-case` — related step in the journey
- `ortho-skill-assessment` — related step in the journey
- `ortho-case-report-publishing` — related step in the journey
