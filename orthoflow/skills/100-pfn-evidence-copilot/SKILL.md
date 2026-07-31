---
name: ortho-pfn-evidence-copilot
description: Conversational, evidence-grounded copilot for proximal femoral nailing (PFN) / intertrochanteric hip fracture surgery. Use this skill whenever a surgeon is planning a PFN case pre-operatively (reviewing X-rays, in an MDT session) or is mid-case and asks a "what should I do next," "is this right," or "what does the evidence say" question about PFN technique, implant choice, reduction quality, or complication avoidance. Grounds every answer in a live PubMed search (via APPICOD query construction) and cross-references the PFN Ontology Schema (step taxonomy + clinical concept taxonomy + verification layer) so answers are tied to a specific step and concept, not generic literature review. Trigger even on terse intraoperative phrasing ("TAD target for this blade?", "entry point looks off, what now?") — mid-case questions need this skill's fast/concise mode, not the general evidence-retrieval skill's full literature-review mode. Educational / decision-support reference only; not medical advice; the responsible clinician decides and signs off in real time.
x-risk-tier: clinical
---

# PFN Evidence Copilot

## Purpose

Give the operating surgeon a fast, evidence-grounded answer to "what do I do next" or "is this
best practice," at two different moments with two different needs:

- **Pre-op / MDT mode** — reviewing X-rays, planning nail length/diameter, discussing approach.
  Slower pace, room for a fuller evidence summary and discussion of trade-offs.
- **Intra-op mode** — a live question mid-case. Needs a short, direct, citation-backed answer in
  seconds, not a literature review. Optimize for brevity; offer to expand only if asked.

This skill is the **real-time consumer** of two things built earlier: the PFN Ontology Schema
(`references/pfn-ontology-schema.md`) for structured clinical vocabulary, and a PubMed
Entrez-API-driven, APPICOD-structured query methodology (`references/pubmed-eutils-openapi.yaml`)
for grounding every answer in retrievable literature — not model memory.

It is a specialization of `ortho-evidence-retrieval`, not a replacement for it: use this skill for
live, case-anchored PFN questions; use `ortho-evidence-retrieval` for open-ended literature reviews
with no active case.

## When to use

Trigger this skill when:

- A surgeon is reviewing pre-op imaging or in an MDT and asks a planning question ("long or short
  nail here?", "is this pattern stable enough for a standard entry point?")
- A surgeon is mid-case and asks a direct, often terse question ("TAD target for this blade?",
  "posterior sag on lateral, what now?", "should I lock static or dynamic here?")
- A question implicitly asks "am I following best practice" — even phrased as a simple technique
  question, treat it as also asking for the evidence basis
- `ortho-treatment-mapping`, `ortho-implant-selection`, or `ortho-preop-optimisation` want a live
  literature check rather than the standard reference summary

## Workflow

### Step 1 — Classify the question against the PFN Ontology Schema

Before searching anything, map the question to structured context. This is what makes this skill
sharper than a generic "ask PubMed" tool — the query gets built with real clinical scaffolding.

1. Identify the current `step_id` if known (from case context, or ask briefly if genuinely unclear
   and time permits — never ask a clarifying question mid-case if it would stall the surgeon).
2. Identify the closest `concept_id` from the schema's clinical concept taxonomy (§4 of
   `pfn-ontology-schema.md`) — e.g. "TAD target" → `OUT-MECH-TAD`; "posterior sag" →
   `RED-LAT-POSTAVOID`; "hip pin backing out" → `OUT-MECH-ZEFFECT`.
3. If the question doesn't map to an existing `concept_id`, proceed anyway — flag it as a candidate
   for schema extension afterward, don't block the surgeon's answer on ontology completeness.
4. Note `implant_selected` context if known (nail system, head element type) — several concepts in
   the schema are implant-specific (see schema §4, "Implant-specificity flag"), and answering with a
   different system's convention stated as universal is a real failure mode to avoid.

### Step 2 — Build the PubMed query (APPICOD method)

Adapted from the AmA@PubMed methodology, condensed for real-time use:

1. **Translate** the question into formal academic/surgical English.
2. **Decompose into APPICOD**: Age, Population, Pathology, Intervention, Comparison (if any),
   Outcome, Diagnosis (if any) — not every element will be present for a narrow technique question,
   and that's fine.
3. **Extract keywords** per APPICOD element; strip filler words.
4. **Preserve exact nomenclature** (AO/OTA codes, named classifications, implant system names,
   numeric thresholds) — do not synonym-expand these; carry them through unchanged.
5. **Synonym-expand** the remaining keywords: 2 additional medical/surgical synonyms per keyword
   (formal terms, acronyms, age-group synonyms), joined with `OR` inside parentheses. No layman
   terms — synonyms broaden the question, they don't answer it.
6. **Add `NOT` clauses** only if the surgeon's question explicitly excludes something.
7. **Assemble** with explicit parentheses for order of operations; `AND`/`OR`/`NOT` in capitals.

**Field-search syntax note — pick one convention and be consistent:** the AmA@PubMed source
instructions explicitly *forbid* field-search syntax (no `[MeSH]`, `[tiab]`, etc. — plain Boolean
keyword strings only). This differs from `ortho-evidence-retrieval`'s own PubMed pattern, which
*does* use `[MeSH]`/`[PT]`/`[Language]` field tags. **Default to the no-field-tag convention for
this skill** (matches the AmA@PubMed grounding this skill is built on) — but flag this divergence to
whoever maintains both skills, since inconsistent conventions across skills that both hit PubMed is
worth reconciling deliberately rather than accidentally.

### Step 3 — Call PubMed via E-utilities

Using `references/pubmed-eutils-openapi.yaml`:

1. `esearch.fcgi` (`db=pubmed`, `term=<assembled query>`, `retmax` scaled to mode — 3–5 for intra-op,
   up to 10 for pre-op/MDT) → list of PMIDs.
2. `esummary.fcgi` (`retmode=json`) on the returned IDs → titles, authors, journal, pubdate, DOI.
3. `efetch.fcgi` (`retmode=xml`) only when an abstract is needed to confirm relevance or extract a
   specific value (e.g. a stated TAD threshold, a reported complication rate) — skip this call in
   intra-op mode unless the summary alone is insufficient; it's the slowest step.

**Operational notes not in the provided OpenAPI spec but required by NCBI for reliable use:**
- Always send `tool` and `email` query parameters (NCBI usage guidelines).
- Without an API key, E-utilities rate-limits to ~3 requests/second — register a key for anything
  beyond occasional single-case use, since a live case may need 2–3 calls in quick succession.
- If `esearch` returns zero results, broaden by dropping the least-essential synonym cluster before
  giving up — don't return "no evidence found" on an over-constrained first pass.

### Step 4 — Grade the evidence and tie it back to the schema

Use the same grading scale as `ortho-evidence-retrieval` (High/Moderate/Low/Very low). Then apply
the schema's verification layer (§5): if this is an active case and the ontology has tagged the
current segment's `clinical_concepts[].verification_status`, say so explicitly — e.g., "the evidence
target is X; this case's current value is narrated but not yet fluoroscopically confirmed."

### Step 5 — Answer in the right mode

**Intra-op mode** — lead with the direct answer, one or two citations, done. Offer to expand only if
asked. Never make the surgeon read a literature review to get an actionable number.

**Pre-op/MDT mode** — fuller structure is appropriate: evidence summary, contested points if any,
registry data if relevant, then the answer to the specific planning question.

## Output format

**Intra-op (terse):**
```yaml
answer: "Target TAD < 25mm; implant/blade-vs-screw dependent — confirm against your system's IFU."
concept_id: "OUT-MECH-TAD"
evidence_grade: "Moderate"
citations:
  - "Tip–apex distance and predictors of outcome in cephalomedullary nailing — https://pubmed.ncbi.nlm.nih.gov/<pmid>/"
case_verification_status: "narrated_only"   # only if live case context available
not_medical_advice: true
```

**Pre-op/MDT (fuller):**
```yaml
question: "Long vs short PFNA-II for this reverse-obliquity pattern?"
concept_ids: ["INJ-STAB-REVOBL", "IMP-IM-LENGTH"]
what_literature_broadly_supports:
  statement: "Long nails favored for reverse-obliquity/subtrochanteric-extension patterns; lower peri-implant fracture rates."
  confidence: "Moderate"
where_contested:
  - issue: "Short nail acceptability when reverse obliquity is excluded and bone quality is adequate"
implant_specificity_note: "Entry-point and length conventions vary by nail system — confirm against implant_selected.system before treating any threshold as universal."
citations: [<list, each a clickable PubMed URL>]
evidence_grade: "Moderate"
not_medical_advice: true
```

## Example

**Intra-op input:** "TAD target for this blade?"

**Skill response:**
1. Classify: `step_id = PFN-08`, `concept_id = OUT-MECH-TAD`.
2. APPICOD-lite query (technique question, no age/population element needed):
   `("tip apex distance" OR "tip-apex distance" OR "TAD") AND ("cephalomedullary nail" OR "proximal femoral nail" OR "intramedullary nail" OR "PFNA" OR "helical blade") AND ("cut-out" OR "cutout" OR "implant failure" OR "fixation failure")`
3. `esearch` → PMIDs; `esummary` → titles/dates; skip `efetch` (summary sufficient for a threshold
   value already well established in the literature).
4. Answer:
```yaml
answer: "Target TAD < 25mm (Baumgaertner); blade systems tend to tolerate slightly different ranges than screw systems — check your system's IFU for the exact figure."
concept_id: "OUT-MECH-TAD"
evidence_grade: "High"
citations:
  - "Tip–apex distance and predictors of outcome in cephalomedullary nailing — https://pubmed.ncbi.nlm.nih.gov/<pmid>/"
not_medical_advice: true
```

🩺 *Educational reference only — not medical advice. This is a literature-grounded aid to the
surgeon's own real-time judgment, not an instruction. The responsible clinician decides and signs
off, per `ortho-clinical-responsibility`.*

## Safety guardrails

- **Never delay the case.** In intra-op mode, if the query/retrieval pipeline is slow, say so and
  give the surgeon a direct answer from established, well-known thresholds first, with the citation
  to follow — don't make them wait on the API round-trip for a value that's not genuinely in doubt.
- **No fabricated citations, ever.** If `esearch` returns nothing usable, say so plainly — do not
  produce a plausible-sounding reference.
- **"The evidence supports X" is not "do X."** Especially critical in intra-op mode, where a terse
  answer can read as an instruction if not phrased carefully.
- **Flag implant-specificity.** Per the ontology schema's explicit caveat: don't state an
  implant-specific convention (entry point, TAD range, screw configuration) as if it were universal.
- **Route safety-critical situations elsewhere, immediately.** If the question signals a genuine
  emergency (neurovascular compromise, suspected compartment syndrome, major unexpected bleeding),
  this is not a literature-lookup moment — defer to `ortho-red-flag-screen` /
  `ortho-escalation-router` and do not let an evidence search be the bottleneck.
- **Log every intra-op consult.** Route through `ortho-ai-traceability` so there's an audit record
  of what was asked, what was retrieved, and what was answered, tied to `ortho-clinical-responsibility`
  for accountable sign-off.
- **Not medical advice**, stated plainly in every response, not just the fine print.

## Related skills

- `ortho-evidence-retrieval` — the general-purpose version of this skill; use that one for open-ended
  literature review with no active case
- `ortho-aoota-classification`, `ortho-region-classifications` — supply the classification context
  that sharpens query specificity
- `ortho-treatment-mapping`, `ortho-implant-selection`, `ortho-preop-optimisation` — pre-op mode
  consumers of this skill's output
- `ortho-red-flag-screen`, `ortho-escalation-router` — where genuine emergencies get routed instead
- `ortho-ai-traceability`, `ortho-clinical-responsibility` — logging and accountable sign-off for
  every consult this skill produces
- `ortho-case-intake` — supplies `step_id`/`concept_id` context when this skill is invoked mid-case
  from within an active OrthoFlow session rather than standalone
