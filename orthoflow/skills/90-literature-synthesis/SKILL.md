---
name: ortho-literature-synthesis
description: Autonomously searches, reads and synthesises the literature to surface conceptual relationships, contradictions and knowledge gaps — beyond keyword retrieval. Trigger when: a question needs a synthesised view across many studies, not a single lookup. Produces a synthesis with contradictions and gaps, fully cited. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: research
x-archetype: retrieval
x-status: scaffold-v0.4-uncertified
---

# Literature Synthesis

## Purpose

Autonomously searches, reads and synthesises the literature to surface conceptual relationships, contradictions and knowledge gaps — beyond keyword retrieval. It synthesises and surfaces gaps; a human validates the synthesis. Goes beyond ortho-evidence-retrieval (which fetches) to reasoning across the corpus.

## When to use

Trigger this skill when:

- a question needs a synthesised view across many studies, not a single lookup.
- contradictions or knowledge gaps in the literature should be surfaced.

Do **not** use this skill when: as a substitute for systematic review methodology where that is required.

## Workflow

### Step 1 — Search broadly
Internal evidence base + permitted external sources.

### Step 2 — Synthesise
Conceptual relationships, agreements, contradictions.

### Step 3 — Surface gaps
Where evidence is thin or conflicting — with citations.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.synthesise_literature({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
literature_synthesis:
  synthesis: <…>
  contradictions: <…>
  knowledge_gaps: <…>
  citations: <…>
  not_medical_advice: true
```

## Example

**Input:** What does the evidence say on weight-bearing timing after a given fixation?

**Skill response:** Synthesis with two conflicting RCT findings surfaced and a clear evidence gap, all cited.

🩺 *Educational / decision-support reference only — not medical advice. A researcher validates the synthesis.*

## Safety guardrails

- Every claim is traceable to a real source; no fabricated citations (hard veto).
- Surfaces uncertainty and conflict rather than forcing a false consensus.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-evidence-retrieval` — related step in the journey
- `ortho-research-hypothesis` — related step in the journey
- `ortho-study-design-advisor` — related step in the journey
