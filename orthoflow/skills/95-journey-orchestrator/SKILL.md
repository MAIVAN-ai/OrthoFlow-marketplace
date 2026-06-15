---
name: ortho-journey-orchestrator
description: Tracks where the patient is in the journey and sequences the next clinically/administratively appropriate skill — the master orchestrator / state machine + next-best-action. Trigger when: a case must be moved through the journey and the next step chosen. Produces the journey state and the next-best-action. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: meta
x-archetype: decision_gate
x-status: scaffold-v0.4-uncertified
---

# Journey Orchestrator

## Purpose

Tracks where the patient is in the journey and sequences the next clinically/administratively appropriate skill — the master orchestrator / state machine + next-best-action. It coordinates skills (the 'society of mind'/MAS coordinator); it does not itself reason clinically. It delivers agentic BEHAVIOUR via composed skills, not a monolithic agent.

## When to use

Trigger this skill when:

- a case must be moved through the journey and the next step chosen.
- multiple skills must be coordinated toward a goal.

Do **not** use this skill when: to bypass a skill's own human-in-the-loop or escalation gate.

## Workflow

### Step 1 — Determine state
Referral / intake / diagnosis / pre-op / OR / ward / discharge / home / follow-up.

### Step 2 — Select next action
The appropriate skill, respecting gates.

### Step 3 — Hand off
Invoke it; never override its guardrails.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.orchestrate_journey({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
journey_orchestrator:
  journey_state: <…>
  next_best_action: <…>
  gates_respected: <…>
  not_medical_advice: true
```

## Example

**Input:** A case that has just been classified.

**Skill response:** state=diagnosis-complete; next=ortho-differential-reasoning; gates respected.

🩺 *Educational / decision-support reference only — not medical advice. Clinicians own every gated decision the orchestrator routes to.*

## Safety guardrails

- Never overrides a downstream skill's escalation or sign-off gate.
- On any red flag, yields to ortho-escalation-router immediately.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-escalation-router` — related step in the journey
- `ortho-human-signoff` — related step in the journey
- `ortho-role-based-view` — related step in the journey
