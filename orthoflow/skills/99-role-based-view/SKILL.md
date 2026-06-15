---
name: ortho-role-based-view
description: Renders the appropriate view of an output for each audience — patient, surgeon, admin, payer, MedTech, researcher — without leaking what a role shouldn't see. Trigger when: one output must be presented differently (and safely) to different roles. Produces a role-appropriate view with enforced disclosure limits. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: meta
x-archetype: generation
x-status: scaffold-v0.4-uncertified
---

# Role-based View

## Purpose

Renders the appropriate view of an output for each audience — patient, surgeon, admin, payer, MedTech, researcher — without leaking what a role shouldn't see. It tailors presentation and enforces role-appropriate disclosure; it changes no underlying facts.

## When to use

Trigger this skill when:

- one output must be presented differently (and safely) to different roles.
- role-appropriate redaction is needed.

Do **not** use this skill when: to expose data a role is not entitled to see.

## Workflow

### Step 1 — Identify role
The audience.

### Step 2 — Apply disclosure policy
Via the governance gate; redact what the role shouldn't see.

### Step 3 — Render
The role-appropriate view.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.render_role_view({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
role_based_view:
  role_view: <…>
  disclosure_applied: <…>
  redactions: <…>
  not_medical_advice: true
```

## Example

**Input:** An outcome record requested by a MedTech partner.

**Skill response:** Renders a de-identified aggregate view; redacts identifiable fields per policy.

🩺 *Educational / decision-support reference only — not medical advice. Governance policy defines what each role sees.*

## Safety guardrails

- Enforces role-based disclosure via ortho-data-governance-gate; never over-discloses.
- Alters presentation only; never the underlying clinical facts.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-data-governance-gate` — related step in the journey
- `ortho-journey-orchestrator` — related step in the journey
- `ortho-patient-instructions` — related step in the journey
