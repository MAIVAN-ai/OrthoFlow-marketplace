---
name: ortho-second-opinion
description: Orchestrates the OSSOcare second-opinion workflow — anonymise, route, produce a transparent independent opinion with its reasoning trail. Trigger when: a smart second opinion is requested for a case. Produces a structured second-opinion report with a transparent reasoning trail. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: quality
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Second Opinion

## Purpose

Orchestrates the OSSOcare second-opinion workflow — anonymise, route, produce a transparent independent opinion with its reasoning trail. It produces an independent reasoning trail for a clinician to deliver; it does not replace the treating relationship.

## When to use

Trigger this skill when:

- a Smart Second Opinion is requested for a case.
- an independent, transparent review of an existing plan is wanted.

Do **not** use this skill when: to deliver an opinion directly to a patient without clinician oversight.

## Workflow

### Step 1 — Anonymise+intake
Via consent + governance gate.

### Step 2 — Run the reasoning chain
Classification → differential → evidence → options, transparently.

### Step 3 — Produce report
Independent opinion with explicit reasoning, agreements and divergences.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.second_opinion({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
second_opinion:
  second_opinion_report: <…>
  reasoning_trail: <…>
  divergences_from_plan: <…>
  not_medical_advice: true
```

## Example

**Input:** A patient seeks a second opinion on a recommended fusion.

**Skill response:** Independent report agreeing on diagnosis, noting a non-operative option with its evidence; reasoning shown.

🩺 *Educational / decision-support reference only — not medical advice. An overseeing clinician delivers it.*

## Safety guardrails

- Shows the reasoning path, not just a verdict; transparency is the product.
- An overseeing clinician delivers the opinion; consent governs the data.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-differential-reasoning` — related step in the journey
- `ortho-similar-cases` — related step in the journey
- `ortho-consent-management` — related step in the journey
