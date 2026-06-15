---
name: ortho-research-hypothesis
description: Converts patterns in the aggregated data into testable research questions and hypotheses, and identifies where evidence or a product issue may be hiding. Trigger when: aggregated patterns suggest a research question. Produces ranked research hypotheses with rationale. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: educational
x-domain: research
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Research Hypothesis

## Purpose

Converts patterns in the aggregated data into testable research questions and hypotheses, and identifies where evidence or a product issue may be hiding. It proposes hypotheses; humans select and test them. Closes the loop from the commons to research.

## When to use

Trigger this skill when:

- aggregated patterns suggest a research question.
- an evidence/product gap should be turned into a testable hypothesis.

Do **not** use this skill when: to assert causation from observational patterns.

## Workflow

### Step 1 — Surface patterns
From governed aggregated data.

### Step 2 — Form hypotheses
Testable, with rationale.

### Step 3 — Rank
By signal strength and feasibility.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.generate_hypothesis({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
research_hypothesis:
  hypotheses: <…>
  rationale: <…>
  feasibility: <…>
  not_medical_advice: true
```

## Example

**Input:** An outcome dip linked to one implant class in the commons.

**Skill response:** A ranked hypothesis with rationale and a feasibility note; flagged for human testing.

🩺 *Educational / decision-support reference only — not medical advice. A researcher selects and tests it.*

## Safety guardrails

- Frames associations as hypotheses, never as established causation.
- Operates only on governed, de-identified aggregate data.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-literature-synthesis` — related step in the journey
- `ortho-study-design-advisor` — related step in the journey
- `ortho-implant-outcome-link` — related step in the journey
