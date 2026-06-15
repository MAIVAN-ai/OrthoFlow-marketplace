---
name: ortho-human-signoff
description: Routes outputs to the right human (surgeon, nurse, admin, payer, patient, cooperative reviewer) for sign-off and records it. Trigger when: an output requires a specific human's sign-off before it proceeds. Produces a sign-off request and, once signed, the record. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: meta
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Human Sign-off

## Purpose

Routes outputs to the right human (surgeon, nurse, admin, payer, patient, cooperative reviewer) for sign-off and records it. It manages sign-off routing; the human signs.

## When to use

Trigger this skill when:

- an output requires a specific human's sign-off before it proceeds.
- sign-off must be recorded for accountability.

Do **not** use this skill when: to mark anything signed that a human did not sign.

## Workflow

### Step 1 — Determine signer
By output type and tier.

### Step 2 — Request sign-off
Present the output for review.

### Step 3 — Record
On sign, write the responsibility record.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.request_signoff({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
human_signoff:
  signoff_request: <…>
  signer_role: <…>
  signed_record: <…>
  not_medical_advice: true
```

## Example

**Input:** A discharge-summary draft awaiting sign-off.

**Skill response:** Routes to the discharging clinician; records the signature on approval.

🩺 *Educational / decision-support reference only — not medical advice. The designated human signs.*

## Safety guardrails

- Never auto-signs; absence of a signature blocks the output.
- Prescribing/clinical-tier outputs require the appropriate qualified signer.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-clinical-responsibility` — related step in the journey
- `ortho-journey-orchestrator` — related step in the journey
- `ortho-ai-traceability` — related step in the journey
