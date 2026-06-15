---
name: ortho-clinical-responsibility
description: Ensures every AI output has a named human owner and makes the liability chain explicit — developer vs. provider vs. institution. Trigger when: an ai output will influence care and must have a named accountable human. Produces a responsibility record with the accountability chain. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: governance
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Clinical Responsibility

## Purpose

Ensures every AI output has a named human owner and makes the liability chain explicit — developer vs. provider vs. institution. It records accountability; it does not assign legal liability (that is for law). It answers the literature's liability concern by making the chain explicit.

## When to use

Trigger this skill when:

- an AI output will influence care and must have a named accountable human.
- the responsibility/liability chain must be made explicit.

Do **not** use this skill when: to let an output proceed without a named human owner.

## Workflow

### Step 1 — Identify owner
The named clinician accountable for the output.

### Step 2 — Make chain explicit
Developer / provider / institution roles for this output.

### Step 3 — Gate
Block clinical use of any unsigned output.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.record_responsibility({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
clinical_responsibility:
  responsibility_record: <…>
  accountable_human: <…>
  liability_chain: <…>
  not_medical_advice: true
```

## Example

**Input:** A treatment-mapping output about to inform a plan.

**Skill response:** Records the signing surgeon as owner; makes the developer/provider/institution chain explicit.

🩺 *Educational / decision-support reference only — not medical advice. The named clinician is accountable.*

## Safety guardrails

- No clinical output proceeds without a named accountable human.
- Surfaces the liability chain transparently; especially where reasoning is uncertain.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-ai-traceability` — related step in the journey
- `ortho-human-signoff` — related step in the journey
- `ortho-regulatory-escalation` — related step in the journey
