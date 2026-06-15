---
name: ortho-ai-traceability
description: Logs model, prompt, sources, confidence, and human reviewer for every AI output — the audit trail. Trigger when: any ai output is produced and must be auditable. Produces an immutable per-output audit record. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: governance
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# AI Traceability

## Purpose

Logs model, prompt, sources, confidence, and human reviewer for every AI output — the audit trail. It records the trail; it does not make decisions.

## When to use

Trigger this skill when:

- any AI output is produced and must be auditable.
- a traceability record is required for governance/regulation.

Do **not** use this skill when: to reconstruct a trail that was not actually captured.

## Workflow

### Step 1 — Capture provenance
Model, version, prompt, sources, confidence.

### Step 2 — Record reviewer
The human who signed off.

### Step 3 — Seal
Swiss Trust Layer attestation.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.trace_output({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
ai_traceability:
  trace_record: <…>
  model_provenance: <…>
  human_reviewer: <…>
  not_medical_advice: true
```

## Example

**Input:** An AO/OTA classification output accepted by a surgeon.

**Skill response:** Trace record: model+version+sources+confidence+signing surgeon; sealed.

🩺 *Educational / decision-support reference only — not medical advice. Governance reads the trail.*

## Safety guardrails

- Records only what actually occurred; no retrospective fabrication.
- Tamper-evident via the Swiss Trust Layer.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-clinical-responsibility` — related step in the journey
- `ortho-data-governance-gate` — related step in the journey
- `ortho-journey-orchestrator` — related step in the journey
