---
name: ortho-operative-note
description: Drafts a structured, codeable operative note from intra-operative findings for surgeon edit and sign-off. Trigger when: an operative note draft is needed after a procedure. Produces a structured operative-note draft. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: periop
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Operative Note

## Purpose

Drafts a structured, codeable operative note from intra-operative findings for surgeon edit and sign-off. It drafts; the surgeon edits and signs. Nothing is finalised without sign-off.

## When to use

Trigger this skill when:

- an operative note draft is needed after a procedure.
- a structured, codeable note must be generated from findings.

Do **not** use this skill when: to finalise or submit a note without surgeon sign-off.

## Workflow

### Step 1 — Gather findings
Approach, implants, intra-op findings, deviations from plan.

### Step 2 — Draft structured note
In the house template, codeable, with placeholders for missing detail.

### Step 3 — Present for sign-off
Surgeon edits and signs.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.draft_op_note({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
operative_note:
  operative_note_draft: <…>
  coding_relevant_facts: <…>
  missing_fields: <…>
  not_medical_advice: true
```

## Example

**Input:** Intramedullary nailing of a tibial shaft fracture, findings dictated.

**Skill response:** Structured draft with implant sizes as placeholders where not dictated; presented for sign-off.

🩺 *Educational / decision-support reference only — not medical advice. The surgeon edits and signs the note.*

## Safety guardrails

- Never invents operative detail; missing facts are left as explicit placeholders.
- Draft only — requires surgeon sign-off before it enters the record.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-clinical-coding` — related step in the journey
- `ortho-discharge-summary` — related step in the journey
- `ortho-case-report-publishing` — related step in the journey
