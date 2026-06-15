---
name: ortho-referral-triage
description: Reads an incoming referral (letter, imaging report, comorbidities, urgency signals) and assigns an urgency class and the correct clinical pathway before a slot is booked. Trigger when: a gp/ed referral or second-opinion request arrives and must be prioritised and routed. Produces an urgency class plus the recommended subspecialty pathway. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: core
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Referral Triage

## Purpose

Reads an incoming referral (letter, imaging report, comorbidities, urgency signals) and assigns an urgency class and the correct clinical pathway before a slot is booked. It is the front door above case intake; it routes, it does not diagnose.

## When to use

Trigger this skill when:

- a GP/ED referral or second-opinion request arrives and must be prioritised and routed.
- a case must be classified emergency / urgent / elective / second-opinion.

Do **not** use this skill when: a case is already in clinic and intake has begun — route to ortho-case-intake.

## Workflow

### Step 1 — Parse the referral
Extract problem, mechanism, duration, comorbidities, red-flag signals.

### Step 2 — Assign urgency
emergency / urgent / elective / second-opinion, with the deciding factors named.

### Step 3 — Route subspecialty
trauma, arthroplasty, spine, foot/ankle, sports, tumour, paediatric.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.triage_referral({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
referral_triage:
  urgency_class: <…>
  subspecialty_pathway: <…>
  red_flags_detected: <…>
  why: <…>
  not_medical_advice: true
```

## Example

**Input:** A faxed GP referral: 68F, atraumatic worsening thigh pain, night pain, weight loss.

**Skill response:** Flags possible pathological lesion → urgency=urgent, pathway=tumour, routed to ortho-red-flag-screen before scheduling.

🩺 *Educational / decision-support reference only — not medical advice. The clinician confirms urgency and pathway.*

## Safety guardrails

- Error asymmetry: under-triage (calling an emergency elective) is the catastrophic direction — bias toward escalation when uncertain.
- Hands any red flag straight to ortho-red-flag-screen; never downgrades a red flag.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-red-flag-screen` — related step in the journey
- `ortho-case-intake` — related step in the journey
- `ortho-case-completeness` — related step in the journey
