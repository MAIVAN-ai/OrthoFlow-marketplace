---
name: ortho-case-completeness
description: Checks whether an uploaded case has the minimum data — history, imaging, labs, medication, consent, prior surgery — for safe downstream reasoning. Trigger when: a case is submitted for second opinion, qa, or registry and may be missing required data. Produces a completeness verdict and a missing-data checklist. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: core
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Case Completeness

## Purpose

Checks whether an uploaded case has the minimum data — history, imaging, labs, medication, consent, prior surgery — for safe downstream reasoning. It gates on completeness; it does not assess clinical quality of content.

## When to use

Trigger this skill when:

- a case is submitted for second opinion, QA, or registry and may be missing required data.
- you need a missing-data checklist before proceeding.

Do **not** use this skill when: completeness has already been confirmed this session.

## Workflow

### Step 1 — Check required elements
Against the use-case profile (second opinion / QA / PMCF have different minima).

### Step 2 — Produce checklist
List what is missing and who can supply it.

### Step 3 — Gate
complete / hold-for-data / escalate.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.check_completeness({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
case_completeness:
  completeness: <…>
  missing_items: <…>
  blocking: <…>
  not_medical_advice: true
```

## Example

**Input:** A second-opinion submission with imaging but no consent record.

**Skill response:** Verdict=hold; missing=[consent-for-second-opinion]; routed to ortho-consent-management.

🩺 *Educational / decision-support reference only — not medical advice. The submitting clinician supplies missing items.*

## Safety guardrails

- Defaults to hold when a safety-relevant element (imaging, consent) is missing.
- Does not infer missing clinical facts to 'fill in' a case.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-ehr-normalisation` — related step in the journey
- `ortho-case-intake` — related step in the journey
- `ortho-consent-management` — related step in the journey
