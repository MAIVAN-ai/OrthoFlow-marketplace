---
name: ortho-hospital-qa-benchmark
description: Benchmarks complication rates and outcomes against internal baselines and detects outliers — the Hospital-QA product. Trigger when: site/surgeon outcomes must be benchmarked. Produces a benchmark dashboard with risk-adjusted outliers. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: administrative
x-domain: quality
x-archetype: ranking_differential
x-status: scaffold-v0.4-uncertified
---

# Hospital QA Benchmark

## Purpose

Benchmarks complication rates and outcomes against internal baselines and detects outliers — the Hospital-QA product. It surfaces benchmarks and outliers; humans investigate. It does not attribute blame.

## When to use

Trigger this skill when:

- site/surgeon outcomes must be benchmarked.
- complication-rate outliers need detection.

Do **not** use this skill when: to draw punitive conclusions without case-level review.

## Workflow

### Step 1 — Aggregate
Risk-adjusted outcomes and complications.

### Step 2 — Benchmark
Against internal baseline.

### Step 3 — Detect outliers
With confidence, for human investigation.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.benchmark_qa({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
hospital_qa_benchmark:
  benchmark: <…>
  outliers: <…>
  risk_adjustment: <…>
  not_medical_advice: true
```

## Example

**Input:** Quarterly SSI rates across surgeons.

**Skill response:** Risk-adjusted benchmark; one outlier flagged for case-level review.

🩺 *Educational / decision-support reference only — not medical advice. QA governance investigates outliers.*

## Safety guardrails

- Risk-adjusted; raw-rate outliers are not presented as performance judgments.
- Outliers trigger investigation, never automatic attribution.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-pathway-adherence` — related step in the journey
- `ortho-mm-review` — related step in the journey
- `ortho-outcome-measurement` — related step in the journey
