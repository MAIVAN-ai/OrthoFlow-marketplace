# PubMed / PMC Retrieval Reference

Technical recipes and licensing rules for the `ortho-similar-cases` skill.
All endpoints below are NCBI public services. **Endpoints and filter tokens evolve — verify against the current NCBI E-utilities and PMC tooling docs before relying on them in production.** (NCBI announced changes to several legacy PMC FTP/Cloud download paths taking effect in 2026.)

NCBI etiquette: include `&tool=ortho-x&email=<contact>` on every request; without an API key the rate limit is 3 requests/sec, with an `&api_key=` it is 10/sec. Batch IDs, don't loop one-at-a-time.

---

## 1. The base corpus query

This skill is built on top of the ORTHO-X orthopaedic case-report corpus. The seed query (from the PubMed web UI) is:

```
term:   "orthopedic procedures"[MeSH Terms] OR "orthopedic procedures/adverse effects"[MeSH Terms]
filter: simsearch2.ffrft     → Free full text
filter: pubt.casereports     → Case Reports (publication type)
sort:   pubdate              → newest first
```

**Web-UI filter token → E-utilities `term` clause mapping:**

| Web UI filter | `term` clause to AND into esearch |
|---|---|
| `simsearch2.ffrft` (Free full text) | `"free full text"[sb]` |
| `pubt.casereports` (Case Reports) | `casereports[Filter]` (equivalently `"case reports"[Publication Type]`) |
| `simsearch1.fha` (Full text, any) | `"full text"[sb]` |

So the seed query as an E-utilities `term` is:

```
( "orthopedic procedures"[MeSH Terms] OR "orthopedic procedures/adverse effects"[MeSH Terms] )
AND "free full text"[sb]
AND casereports[Filter]
```

The skill **narrows** this seed by AND-ing the case fingerprint (anatomy + classification + features) — see §2.

> Sort token: the web UI uses `sort=pubdate`; E-utilities `esearch` historically uses `sort=pub_date`. Tokens differ between the UI and the API — check the current esearch parameter list. Newest-first is a tie-breaker, not the ranking (ranking is by clinical similarity, §4).

---

## 2. ESearch — get the candidate PMIDs

```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi
  ?db=pubmed
  &retmode=json
  &retmax=40
  &sort=pub_date
  &term=( ( "orthopedic procedures"[MeSH Terms] OR "orthopedic procedures/adverse effects"[MeSH Terms] )
          AND "free full text"[sb]
          AND casereports[Filter] )
        AND ( <FINGERPRINT> )
  &tool=ortho-x&email=<contact>&api_key=<key>
```

`<FINGERPRINT>` is composed from the classification result, e.g. for a proximal-femur 31-A2:

```
( "femoral neck fractures"[MeSH] OR "hip fractures"[MeSH]
  OR intertrochanteric OR pertrochanteric OR "31-A2" OR "31A2" OR "AO/OTA 31" )
```

Returns `esearchresult.idlist` = the candidate PMIDs (URL-encode the term in practice).

---

## 3. Map PMIDs → PMC (which candidates have reusable full text + figures)

PubMed records have abstracts only. **Figures live in PubMed Central (PMC).** Map with ELink:

```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/elink.fcgi
  ?dbfrom=pubmed&db=pmc&retmode=json
  &id=<pmid1>,<pmid2>,...
  &tool=ortho-x&email=<contact>
```

A PMID with a linked PMCID *may* have retrievable figures. A PMID with **no** PMCID has no PMC full text → link-out only.

Citation metadata for display comes from ESummary:

```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esummary.fcgi?db=pubmed&retmode=json&id=<pmids>
```

---

## 4. Similarity ranking (transparent, classification-driven)

Ranking is by **clinical/structural similarity**, not recency. Score each candidate:

| Signal | Match → weight |
|---|---|
| AO/OTA subgroup (e.g. `31-A2.1`) | exact = **strong** |
| AO/OTA group (`31-A2`) | match = **strong** |
| AO/OTA type (`31-A`) | match = medium |
| AO/OTA segment (`31`) | match = low-medium |
| Region eponym (Garden / Schatzker / Neer …) | match = **strong** boost |
| Mechanism (high- vs low-energy) | match = minor boost |
| Implant / complication (for the *adverse-effects* arm) | match = strong boost when the question is complication-focused |
| Recency | tie-breaker only |

Report a **match tier** (Exact / Close / Related / Loose) plus an indicative `similarity` 0–1, and always state *why similar* and *what's different*. Never present a similar case as if it were the same case.

**Optional visual re-ranking.** If the ORTHO-X VLM layer (VSS / Cosmos) is connected, the text-retrieved shortlist can be re-ranked by visual similarity of the radiographs. Text + classification retrieval is the reliable baseline; the VLM re-rank is an enhancement, never the sole filter.

---

## 5. Figures — captions and image bytes

**Structured full text + figure captions** (PMC OA articles) via the BioC API:

```
https://www.ncbi.nlm.nih.gov/research/bionlp/RESTful/pmcoa.cgi/BioC_json/PMC<id>/unicode
```

**Figure image files** for OA-subset articles via the OA Web Service (returns a package, `.tar.gz`, containing the figure binaries, plus the article's **license tag**):

```
https://www.ncbi.nlm.nih.gov/pmc/utils/oa/oa.fcgi?id=PMC<id>
```

For a human link-out, the figure page is:

```
https://pmc.ncbi.nlm.nih.gov/articles/PMC<id>/figure/<figure-id>/
```

---

## 6. ⚖️ The figure-licensing gate (the load-bearing guardrail)

**PubMed "Free full text" (`ffrft`) means free to *read*, NOT free to *reuse*.** Reusability is decided **only** by the PMC Open-Access license tag returned by the OA service / FTP `oa_file_list`. Resolve the license *before* showing any figure inline.

| PMC OA group / license tag | Inline display? | Conditions |
|---|---|---|
| `oa_comm` — **CC0, CC BY** | ✅ Yes | Display + reuse (incl. commercial) with attribution |
| `oa_noncomm` — **CC BY-NC, CC BY-NC-SA** | ✅ Yes (educational / non-commercial) | Attribution; not for commercial use |
| **CC BY-NC-ND / any -ND** | ✅ unmodified only | Attribution; **no cropping, annotating, or overlaying** (that creates a derivative) |
| `oa_other` — **"NO-CC CODE" / custom** | ❌ No | Link out to the PMC article; do not re-host the image |
| **`author_manuscript`** (free-to-read manuscript) | ❌ No | Link out; figures are typically all-rights-reserved |
| **Not in PMC** (free via publisher only) | ❌ No | Link out to the publisher |

Rules that follow from the table:
- Default to **link-out** whenever the license is unknown, ambiguous, or unresolved. Never guess in favour of display.
- Every displayed figure carries an **attribution caption**: authors, year, journal, PMCID, and the license string (e.g. "CC BY 4.0").
- Annotating/cropping a figure (e.g. drawing the fracture line) is a **derivative** — only permitted for licenses without `-ND`.
- The skill surfaces *published* literature figures. It does **not** re-publish or re-host the user's own patient images anywhere; the patient's images stay inside the case record.
