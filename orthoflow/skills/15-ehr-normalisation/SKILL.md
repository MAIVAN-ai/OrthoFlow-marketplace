---
name: ortho-ehr-normalisation
description: Standardises messy, largely unstructured EHR inputs (free-text notes, scanned labs, prior records) into the structured case record every downstream skill depends on. Trigger when: raw ehr/pacs data must be turned into a structured case record before reasoning. Produces a normalised, schema-validated case record with provenance per field. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off.
x-risk-tier: clinical
x-domain: core
x-archetype: extraction_structuring
x-status: scaffold-v0.4-uncertified
---

# EHR Normalisation

## Purpose

Standardises messy, largely unstructured EHR inputs (free-text notes, scanned labs, prior records) into the structured case record every downstream skill depends on. It harmonises data; it does not interpret it clinically. Addresses the ~80%-unstructured-EHR problem named in the literature.

## When to use

Trigger this skill when:

- raw EHR/PACS data must be turned into a structured case record before reasoning.
- fields are scattered across free text and need normalising (units, laterality, dates, meds).

Do **not** use this skill when: the data is already structured and validated — proceed to ortho-case-intake.

## Workflow

### Step 1 — Ingest
Pull free-text notes, labs, medication lists, prior imaging reports via internal connectors only.

### Step 2 — Normalise
Map to the canonical schema: units, laterality, dates, coded problems; attach source provenance.

### Step 3 — Flag low-confidence fields
Mark fields needing human verification rather than silently guessing.

### Step 4 — Call the MCP backend (if available)
```
orthoclass.normalise_ehr({ case_id: <id>, ... })
```
Otherwise, return the structured output below and the manual pathway.

## Output format

```yaml
ehr_normalisation:
  normalised_record: <…>
  provenance_per_field: <…>
  low_confidence_fields: <…>
  not_medical_advice: true
```

## Example

**Input:** A scanned op note + an unstructured medication list for a revision TKA patient.

**Skill response:** Returns a structured record; flags 'laterality ambiguous in source' for human confirmation rather than assuming.

🩺 *Educational / decision-support reference only — not medical advice. A clinician verifies flagged fields.*

## Safety guardrails

- Critical fields (laterality, allergies, anticoagulation) that cannot be normalised with confidence are flagged for human verification, never guessed.
- Every normalised field carries provenance back to its source; no field is fabricated.
- **No fabricated citations or data.** If retrieval or a required input is missing, say so rather than inventing it.
- **Not medical advice.**

## Related skills

- `ortho-case-intake` — related step in the journey
- `ortho-case-completeness` — related step in the journey
- `ortho-patient-history-summarisation` — related step in the journey
