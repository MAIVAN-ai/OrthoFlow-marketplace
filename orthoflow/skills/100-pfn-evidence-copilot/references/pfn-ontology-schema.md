# Surgical Procedure Ontology Schema — PFN / Intertrochanteric Femur Fracture (v2)

Consolidated from the original schema plus confirmed findings across three mapped videos and one
evidence-based literature appraisal (AmA@PubMed assessment of video 3). Supersedes v1.

Four layers, kept separate because they answer different questions and come from different sources:

1. **Step taxonomy** (§2) — temporal: what's happening, in order (order is surgeon/system-variable).
2. **Clinical concept taxonomy** (§4) — topical: the judgment criteria surgeons reason with.
3. **Verification layer** (§5) — *new*: was a concept merely narrated, or actually confirmed/measured?
4. **Perioperative care taxonomy** (§6) — *new*: evidence-based systemic care that is structurally
   outside video scope, sourced from EHR/records, not narration.

---

## 1. Procedure-level metadata

```json
{
  "procedure_id": "uuid",
  "procedure_type": "PFN",
  "procedure_type_full": "Proximal Femoral Nailing (intertrochanteric fracture)",
  "aoota_code": "31-A2",
  "region_classification": null,
  "mechanism_of_injury": "domestic fall",
  "patient_context": { "age": 47, "sex": "male" },
  "injury_pattern_note": "subtrochanteric extension",
  "transcript_provenance": "verbatim_asr",   // verbatim_asr | human_transcribed | reconstructed_paraphrase
                                                // reconstructed_paraphrase sources: usable for step-taxonomy
                                                // validation, EXCLUDED by default from rationale training pairs
  "implant_selected": {
    "category": "intramedullary_nail",
    "system": "PFNA2",
    "nail_length": "short",
    "head_element": ["helical_blade"],          // now an ARRAY — multi-component fixation (e.g. lag
                                                   // screw + separate anti-rotation pin) is the norm, not
                                                   // the exception; use ["unclear"] if not determinable
    "combined_entry_reamer_tool": false,         // NEW — some systems use one instrument for both;
                                                   // determines whether PFN-04/PFN-05 are one pass or two
    "augmentation_planned": false
  },
  "surgeon_id": "pseudonymized-id",
  "recording_type": "surgeon_pov",
  "narration_present": true,
  "consent_scope": {
    "care": true, "research": true,
    "secondary_use_ai_training": true, "commons_eligible": true
  },
  "source_language": "de-CH",
  "deidentification_status": "verified"
}
```

---

## 2. Step taxonomy — PFN (temporal, order is variable)

`step_id` numbering is a **catalog**, not an assumed sequence — record actual order performed via
`sequence_index` on each segment (§3). Confirmed across 3 independent videos: `PFN-04b` (canal
guidewire) is a real, recurring step that was missing from v1.

| step_id | Step | Conditional on |
|---|---|---|
| `PFN-00` | Pre-op planning (imaging/CT review, nail length/diameter selection) | often absent from narration |
| `PFN-01` | Patient positioning & traction table setup | — |
| `PFN-02` | Entry point localization & incision | — |
| `PFN-03` | Provisional/closed reduction (AP + lateral) | — |
| `PFN-04` | Opening femur (entry point creation) | may be combined with `PFN-05` — see `combined_entry_reamer_tool` |
| `PFN-04b` | **Canal guidewire passage** (after entry, before reaming) | confirmed 3/3 videos — treat as standard, not optional |
| `PFN-05` | Entry/canal reaming | — |
| `PFN-06` | Nail insertion | — |
| `PFN-07` | Proximal (neck) guidewire placement — all head-element trajectories | — |
| `PFN-08` | Head element insertion (screw/blade/multi-component) | order relative to `PFN-09` is surgeon-variable |
| `PFN-09` | Anti-rotation element placement | if two-component system; may precede `PFN-08` |
| `PFN-10` | Distal locking (static/dynamic, freehand or jig-guided) | — |
| `PFN-11` | Cement augmentation | if `augmentation_planned = true` |
| `PFN-12` | End cap placement | — |
| `PFN-13` | Final fluoroscopic check | see `final_check_checklist`, §3 |
| `PFN-14` | Wound closure | — |

---

## 3. Segment tag schema (per video chunk)

```json
{
  "segment_id": "uuid",
  "procedure_id": "uuid",
  "step_id": "PFN-08",
  "sequence_index": 9,                 // NEW — actual order in this case, since step_id order isn't fixed
  "content_type": "procedural_action", // NEW — procedural_action | implant_description | chapter_preview | context
  "time_start": 842.3,
  "time_end": 871.9,
  "transcript_raw": "...",
  "transcript_language": "de-CH",

  "entities": [
    { "text": "helical blade", "type": "implant", "ontology_ref": null }
  ],

  "clinical_concepts": [
    {
      "concept_id": "OUT-MECH-TAD",
      "domain": "outcomes_complications",
      "stated_value": "18mm",
      "reference_standard": "~25mm threshold commonly cited; implant/blade-vs-screw dependent",  // NEW
      "evidence_refs": ["Tip–apex distance and predictors of outcome in cephalomedullary nailing"], // NEW
      "verification_status": "narrated_only",   // NEW — see §5
      "notes": ""
    }
  ],

  "reasoning": {
    "observation": "...", "decision": "...", "rationale": "...",
    "risk_flag": null, "decision_point": null
  },

  "final_check_checklist": null,       // NEW — populate only on PFN-13 segments, see §3 note below

  "equipment_note": null,              // NEW — operational/equipment issues, distinct from clinical risk

  "visual_quality": { "view_adequate": true, "occlusion": false, "notes": "" },
  "provenance": { "asr_confidence": 0.91, "ontology_mapping_method": "manual_review", "reviewed_by": null }
}
```

`final_check_checklist` (populate on `PFN-13` segments): a fixed array recurring closely enough
across independent videos to formalize rather than parse from free text each time —
`fracture_reduction_satisfactory`, `nail_position_appropriate`, `hip_screw_or_blade_position_correct`,
`distal_locking_screw_placement_correct`, `no_intra_articular_penetration`,
`no_implant_impingement`, `limb_length_and_rotation_acceptable`. Each item: `stated` | `confirmed` |
`not_addressed` (ties into §5 verification layer).

---

## 4. Clinical concept taxonomy

Same four domains as v1 (`INJ-*`, `RED-*`, `IMP-*`, `OUT-*`), with confirmed additions:

### New in Reduction Principles
| concept_id | Concept |
|---|---|
| `RED-COMPRESSION-TRACTION` | Controlled compression via traction during screw/blade insertion — confirmed independently in 2 of 3 videos |
| `RED-ROTATION-NEUTRAL` | Neutral limb rotation confirmed before distal locking |

### New in Outcomes & Complications
| concept_id | Concept |
|---|---|
| `OUT-MECH-JIGLOOSENESS` | Jig/nail connection looseness → screw-hole trajectory mismatch |
| `OUT-REDLOSS-ROTATION` | Postoperative rotational malalignment (previously entirely absent — only varus/shortening existed) |
| `OUT-SAFETY-RADIATION` | Radiation exposure minimization (patient + OR staff) |
| `OUT-MECH-JOINTPENETRATION` | Intraoperative articular penetration — **distinct from** `OUT-MECH-CUTOUT` (delayed mechanical failure vs. immediate technical error) |
| `OUT-MECH-CORTEXBREACH` | Lateral/outer cortex breach during drilling/reaming |

### Tentative — needs more data before treating as settled
- `TECH-*` domain (named reusable technique maneuvers — "perfect circle" freehand locking alignment,
  reduction-aid/joystick use, internal rotation for lateral reduction). Only one video strongly
  motivated this; hold as a candidate, not yet a confirmed domain.

### Implant-specificity flag
The evidence appraisal (see below) flagged that entry-point criteria phrased as universal rules
(e.g. "posterior 1/3–anterior 2/3 junction on lateral") are actually **implant-design-dependent**,
not generalizable technique. Add `implant_specific: true` to any concept where this applies, and
cross-reference `implant_selected.system` — don't let implant-specific technique get encoded as if
it generalizes across nail systems.

---

## 5. Verification layer (new — from evidence-based appraisal)

The single most important addition from the PubMed-literature appraisal of video 3: **a technique
can be narrated correctly while the actual result is unverified or suboptimal** — "a narrated
technique can sound correct while the actual fluoroscopic result is suboptimal." The schema was
conflating *claimed* and *confirmed*. Every `clinical_concepts[]` entry now carries:

```json
"verification_status": "narrated_only"
```//  values: narrated_only | fluoroscopically_confirmed | measured | not_addressed

This matters directly for anything resembling a SurgiNaut adherence-certification use case: a
protocol-adherence check needs to distinguish "surgeon said he checked X" from "X was actually
confirmed on imaging with a stated value" — collapsing these would let a well-narrated but
under-documented case pass as if it were fully verified.

**Reference standards vs. case values:** quantitative concepts (e.g. `OUT-MECH-TAD`) should carry
both a case-specific `stated_value` (what this surgeon reported, if anything) and a literature-
sourced `reference_standard` (the evidence-based target range) — these are different things and were
previously conflated into one field.

**Verification methods, not just one technique:** for concepts like `OUT-REDLOSS-ROTATION`, the
appraisal lists multiple valid confirmation methods (foot/patella neutrality, lesser-trochanter
profile comparison, cortical width/shape comparison, fluoroscopic views, direct inspection,
postoperative CT) — a case narrating only one method doesn't mean the others weren't used; record
which method(s) were *stated*, not assume completeness.

---

## 6. Perioperative / systemic care taxonomy (new — `PERI-*`)

The appraisal explicitly lists evidence-based hip-fracture-care measures that **cannot be assessed
from surgical video at all**, because they occur outside the recorded window (pre-op, ward-based,
post-op): antibiotic prophylaxis, VTE prophylaxis, surgical timing, anaesthetic/geriatric
optimisation, tranexamic acid, pressure-area/traction-table protection, postop Hb/wound monitoring,
early mobilisation, weight-bearing instructions, osteoporosis evaluation, nutritional assessment,
delirium prevention, falls-risk evaluation, postop radiograph/union follow-up.

**Structural implication:** these belong at the **procedure/case level**, sourced from EHR/records
(via `ortho-ehr-normalisation`, `ortho-preop-optimisation`, `ortho-vte-prophylaxis-planning`,
`ortho-discharge-summary`), not from video-tagging at all. A "best practice" certification that only
consumes video-derived tags will always be structurally incomplete — flag this explicitly rather
than let a high video-tag completeness score imply overall best-practice compliance.

```json
"perioperative_care": {
  "source": "ehr",                 // never "video" — always flag if populated from narration by mistake
  "antibiotic_prophylaxis": null,
  "vte_prophylaxis": null,
  "surgical_timing_appropriate": null,
  "tranexamic_acid_used": null,
  "early_mobilisation": null,
  "osteoporosis_evaluation": null,
  "note": "absence of data here does not imply these did not occur clinically — only that this source cannot confirm them"
}
```

---

## 7. Entity type taxonomy

Unchanged from v1: `anatomy`, `instrument`, `implant`, `action`, `finding`, `measurement`.

---

## 8. Downstream consumer note — this is what a SurgiNaut appraisal looks like

The uploaded PubMed-literature appraisal is effectively a worked example of what an automated
certification report *consuming* this schema should look like: per-step verdict
(appropriate/evidence-concordant/limitation), tied to specific `concept_id`s, with `evidence_refs`
and an explicit list of what couldn't be assessed from the source. Worth treating that document's
structure — step-by-step verdict, then a "not assessable from this source" section, then a bottom-
line verdict — as a template for SurgiNaut's actual output format, not just as one-off literature
QA.

---

## 9. Extending to other procedures

Unchanged from v1: duplicate §2 with a new prefix; §3/§7 stay procedure-agnostic; reuse
near-universal `OUT-*` concepts (infection risk, cortex breach, etc.); route through
`ortho-anatomy-routing` for classification metadata; populate §6 from EHR sources regardless of
procedure type.

## 10. Implementation notes

- `step_id` assignment: human-reviewed, especially around ambiguous transitions.
- `clinical_concepts[].concept_id` matching: closed-vocabulary lookup pass after entity NER.
- `consent_scope.secondary_use_ai_training` and `transcript_provenance != reconstructed_paraphrase`
  are both hard gates before any segment is training-pair eligible.
- Prioritize `verification_status = measured` segments for anything feeding
  `ortho-complication-risk-prediction`-style downstream reasoning — narrated-only claims are weaker
  training signal for outcome prediction specifically.
