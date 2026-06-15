---
name: ortho-similar-cases
description: Use this skill after a fracture has been assessed and classified (AO/OTA via skill 04 and/or region-specific via skill 05) to surface visually and clinically similar published case reports — with figures where licensing permits — drawn from a curated PubMed orthopaedic case-report corpus. Trigger on "show me similar cases", "find published examples of this fracture/injury", "has this pattern been reported before", "what does this look like in the literature", or whenever a classified case would benefit from real-world published comparators for teaching, differential sanity-checking, or case-report benchmarking. Retrieval only — educational reference, not medical advice; respects figure copyright (only Open-Access / CC-licensed figures are shown inline, everything else is linked).
x-risk-tier: clinical
---

# Similar Cases

## Purpose

Once a fracture has been assessed and classified, the natural next question is *"has anyone published a case like this, and what did it look like?"* This skill answers that — turning the structured classification into a query against a curated PubMed **case-report** corpus, retrieving the closest published cases, and showing their radiographs **where the licence allows it**.

It is a **retrieval and comparison aid**, not a diagnosis engine and not a treatment precedent. A case that looks like this one in the literature does not transfer its management decision to this patient.

This skill is the **case-level visual neighbour-finder**. It complements `ortho-evidence-retrieval` (skill 09), which answers *"what does the evidence say"* at the population level; this skill answers *"what do comparable individual cases look like"* at the case level.

## When to use

Trigger this skill when:

- AO/OTA classification (skill 04) and/or a region-specific classification (skill 05) is in hand, and the user wants published comparators
- A user asks to "show similar cases", "find published examples", "has this been reported", "what does this look like in the literature"
- Skill 06 (Differential Reasoning) has a contested differential and real published examples of each candidate would help adjudicate it
- Skill 12 (Case Report Publishing) needs comparator cases for the Discussion section
- The question is complication-focused (the corpus includes `orthopedic procedures/adverse effects`, so it is well suited to "published cases of this implant failing / this complication")

Do **not** use this skill before a classification exists — without a classification there is no fingerprint to match. Route through skills 01–05 first.

## The base corpus

This skill is built on the ORTHO-X orthopaedic case-report corpus, seeded by this PubMed query:

```
( "orthopedic procedures"[MeSH Terms] OR "orthopedic procedures/adverse effects"[MeSH Terms] )
AND "free full text"[sb]        ← Free full text (web-UI filter simsearch2.ffrft)
AND casereports[Filter]         ← Case Reports (web-UI filter pubt.casereports)
sorted by publication date
```

The skill **narrows** this seed with the case fingerprint. It never returns the raw seed corpus — that would be tens of thousands of unranked records.

See `references/pubmed-pmc-retrieval.md` for the full E-utilities recipes and the licensing matrix.

## Workflow

### Step 1 — Assemble the case fingerprint

Pull the structured descriptors already produced upstream:

| From | Fingerprint element |
|---|---|
| Skill 03 (Anatomy Routing) | Bone / segment, region |
| Skill 04 (AO/OTA) | AO/OTA code + differentials (the primary search key) |
| Skill 05 (Region classifications) | Eponymous codes (Garden, Pauwels, Schatzker, Neer, Weber…) |
| Skill 01 (Intake) | Mechanism (high- vs low-energy), age band, side |
| Skill 06 (Differential) | Open differentials worth finding examples of |
| Post-op context (if any) | Implant class, complication of interest |

The AO/OTA + region-eponym codes are the **primary key**; everything else refines the match.

### Step 2 — Compose the narrowed query

AND the fingerprint into the seed corpus. Example for a proximal-femur 31-A2:

```
( ( "orthopedic procedures"[MeSH Terms] OR "orthopedic procedures/adverse effects"[MeSH Terms] )
  AND "free full text"[sb] AND casereports[Filter] )
AND ( "hip fractures"[MeSH] OR "femoral neck fractures"[MeSH]
      OR intertrochanteric OR pertrochanteric OR "31-A2" OR "31A2" OR "AO/OTA 31" )
```

Show the user the composed query and the exact ESearch URL so the search is reproducible and inspectable. (NCBI etiquette: `&tool=ortho-x&email=<contact>`, API key for 10 req/s; see the reference file.)

### Step 3 — Retrieve and map to PMC

1. **ESearch** the narrowed query → candidate PMIDs.
2. **ELink** `pubmed → pmc` → which candidates have PubMed Central full text (i.e. potentially have retrievable figures). PMIDs with no PMCID are link-out-only.
3. **ESummary** → citation metadata for display.

### Step 4 — Rank by similarity (not by recency)

Score each candidate transparently on classification match (subgroup > group > type > segment), region-eponym match, mechanism, and — for the adverse-effects arm — implant/complication match. Recency is a tie-breaker only. Assign a **match tier** (Exact / Close / Related / Loose) and an indicative `similarity` 0–1.

**Optional visual re-rank:** if the ORTHO-X VLM layer (VSS / Cosmos) is connected, re-rank the text-retrieved shortlist by radiograph visual similarity. Text + classification retrieval is the reliable baseline; the VLM re-rank is an enhancement, never the sole filter.

### Step 5 — Resolve the figure licence (the gate)

For each shortlisted PMC case, resolve the **Open-Access licence tag** via the OA service *before* deciding how to present it:

| Licence | Presentation |
|---|---|
| CC0 / CC BY (`oa_comm`) | Figure shown **inline** with attribution |
| CC BY-NC / -NC-SA (`oa_noncomm`) | Inline for **educational / non-commercial** use, with attribution |
| any `-ND` | Inline **unmodified only** — no cropping/annotating (that's a derivative) |
| NO-CC / author manuscript / not in PMC | **Link-out only** — do not re-host the image |

**"Free full text" (ffrft) is free to read, not free to reuse.** Default to link-out whenever the licence is unknown or ambiguous. This gate is non-negotiable.

### Step 6 — Pull the representative figure

For displayable cases, fetch one representative figure (the diagnostic radiograph, ideally matching the user's view) plus its caption (BioC) and licence string. For link-out cases, fetch the caption text only and provide the PMC/publisher link.

### Step 7 — Call the MCP backend (if available)

```
orthoclass.find_similar_cases({
  case_id: <id>,
  classification_codes: ["31-A2"],
  region_codes: [],
  corpus: "pubmed_casereports_orthopedics",
  fingerprint: { mechanism: "low_energy", age_band: "elderly", implant: null, complication: null },
  max_results: 8,
  visual_rerank: true,
  require_reusable_figures: false
})
```

Otherwise, return the composed query, the ranked shortlist, and the per-case links so the user can open them directly.

## Output format

```yaml
similar_cases:
  query_used: '(("orthopedic procedures"[MeSH] OR "orthopedic procedures/adverse effects"[MeSH]) AND "free full text"[sb] AND casereports[Filter]) AND ("hip fractures"[MeSH] OR intertrochanteric OR "31-A2")'
  fingerprint:
    ao_ota: "31-A2"
    region_eponym: null
    mechanism: "low_energy"
    age_band: "elderly"
  results:
    - rank: 1
      match_tier: "Close"
      similarity: 0.82
      citation: "<Author et al., Year, Journal>"
      pmid: "<pmid>"
      pmcid: "<PMCxxxxxxx | null>"
      doi: "<doi | null>"
      case_classification: "31-A2 (as reported)"
      why_similar: "Same AO/OTA group; low-energy mechanism in an elderly patient; comparable trochanteric comminution."
      whats_different: "Treated with a sliding hip screw rather than a cephalomedullary nail."
      figure:
        available: true
        license: "CC BY 4.0"
        attribution: "<Author et al., Year, Journal>, PMCID <id>, CC BY 4.0"
        display: "inline"        # inline | link_out
        url_or_link: "<figure image URL | PMC article link>"
    - rank: 2
      match_tier: "Related"
      similarity: 0.61
      citation: "<Author et al., Year, Journal>"
      pmid: "<pmid>"
      pmcid: null
      case_classification: "31-A1 (as reported)"
      why_similar: "Same segment and mechanism; useful contrast for the A1/A2 boundary."
      whats_different: "Simpler two-part pattern; no separate lesser-trochanter fragment."
      figure:
        available: false
        display: "link_out"
        reason: "Not in the PMC Open-Access subset — free to read, not licensed for reuse."
        url_or_link: "<PubMed/publisher link>"
  caveats:
    - "Case reports over-represent the unusual — selection and publication bias apply, especially in the adverse-effects arm."
    - "Similarity is retrieval, not equivalence. A similar published case is not a treatment precedent for this patient."
  not_medical_advice: true
```

## Example

**Input:** Skill 04 returned `31-A2` (confidence 0.74, differential 31-A1) for an elderly low-energy proximal-femur injury. User: *"Show me similar published cases with pictures."*

**Skill response:**

I'll find published case reports closest to this **31-A2** intertrochanteric pattern. Composing the search against the orthopaedic case-report corpus, narrowed to the proximal femur:

```
(("orthopedic procedures"[MeSH Terms] OR "orthopedic procedures/adverse effects"[MeSH Terms])
 AND "free full text"[sb] AND casereports[Filter])
AND ("hip fractures"[MeSH] OR "femoral neck fractures"[MeSH]
     OR intertrochanteric OR pertrochanteric OR "31-A2" OR "AO/OTA 31")
```

I'll rank the hits by classification proximity (exact group first, then type, then segment), keeping the differential `31-A1` in scope for contrast, then resolve each figure's licence before showing anything inline.

**Ranked shortlist (template — actual citations and PMIDs come from live retrieval; nothing here is fabricated):**

| Rank | Tier | Case (as reported) | Why similar | Figure |
|---|---|---|---|---|
| 1 | Close | `<Author, Year>` — 31-A2, elderly, low-energy | same group + mechanism + comminution | inline (CC BY) |
| 2 | Related | `<Author, Year>` — 31-A1, elderly | A1/A2 boundary contrast | link-out (not OA) |
| 3 | Related | `<Author, Year>` — 31-A2 with cut-out | adverse-effects arm: implant complication | inline (CC BY-NC, educational) |

For #1 and #3 the PMC licence permits an educational inline figure (shown with attribution and the licence string). For #2 the article is free to *read* but not licensed for reuse, so I link to it rather than re-hosting the image.

```yaml
similar_cases:
  fingerprint: { ao_ota: "31-A2", mechanism: "low_energy", age_band: "elderly" }
  results:
    - rank: 1
      match_tier: "Close"
      similarity: 0.82
      why_similar: "Same AO/OTA group, low-energy elderly mechanism, comparable comminution."
      whats_different: "SHS rather than CMN."
      figure: { available: true, license: "CC BY 4.0", display: "inline" }
  caveats:
    - "Case reports over-represent the unusual; the adverse-effects hit (#3) is illustrative, not a base-rate."
  not_medical_advice: true
```

🩺 *Educational reference only — not medical advice. Similar published cases are comparison and teaching aids; they do not establish a diagnosis or a treatment plan for this patient. The treating surgeon decides.*

## Safety guardrails

- **Figure-licensing gate.** PubMed "free full text" ≠ free to reuse. Only PMC Open-Access (CC-licensed) figures are shown inline, with attribution and the licence string; everything else is link-out. `-ND` figures are shown unmodified only. Default to link-out when the licence is unknown.
- **Similarity ≠ equivalence.** A similar-looking published case is a retrieval result, not a diagnosis and not a treatment precedent. The skill always states *what's different*, never just *why similar*.
- **Publication / selection bias is named.** Case reports over-represent the rare and the dramatic — especially in the adverse-effects arm. The skill flags this so a complication case is not mistaken for a base-rate.
- **No fabricated citations or PMIDs.** Every citation, PMID, PMCID and DOI must come from live retrieval. If retrieval fails, say so — never invent a reference (consistent with skill 09).
- **Patient privacy preserved.** The skill surfaces *published* literature figures only. It never re-hosts or re-publishes the user's own patient images anywhere; those stay inside the case record.
- **Reproducible search.** The composed query and ESearch URL are always shown so the retrieval can be inspected and re-run.
- **Not medical advice.**

## Related skills

- `ortho-aoota-classification` (04), `ortho-region-classifications` (05) — provide the fingerprint that drives the match
- `ortho-differential-reasoning` (06) — similar published examples of each differential help adjudicate it
- `ortho-evidence-retrieval` (09) — sibling skill: population-level evidence, where this skill is case-level comparators
- `ortho-case-report-publishing` (12) — consumes the similar cases as Discussion comparators
- `ortho-image-quality-check` (02) — the user's own image must pass quality before it is worth comparing to the literature
