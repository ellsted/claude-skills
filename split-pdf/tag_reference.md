---
title: Split-PDF Tag Reference
version: 1.0
maintained_at: Readings/tag_reference.md
last_updated: 2026-03-26
---

This file is the master tag vocabulary for the /split-pdf skill and the future /compare-readings skill. It is loaded automatically by the skill at runtime and should never be embedded in individual notes.md files. All tag definitions, combination rules, and reading profiles are maintained here as the single source of truth; any changes to tag semantics or scope should be made in this file only.

---

## Academic Dimensions (Core 8)

| Tag | Captures | Notes |
|---|---|---|
| [RQ] | Research question and motivation | Always present in synthesis |
| [AUD] | Target audience / sub-community | Always present in synthesis |
| [METHOD] | Overall strategy for answering the research question | Distinct from [STATS] and [DESIGN] |
| [DATA] | Source, unit of observation, sample size, time period | Always present in synthesis |
| [STATS] | Techniques applied to the data | Distinct from [METHOD] and [DESIGN] |
| [FINDINGS] | Main results, key estimates, standard errors | Always present in synthesis |
| [CONTRIB] | What is learned that wasn't known before | Always present in synthesis |
| [REPLICATION] | Public data, archive, appendix, URLs | Rendered as table in synthesis |

---

## Practitioner Extensions

| Tag | Captures | Profile |
|---|---|---|
| [TEACH] | Teachability -- core intuition, appropriate level | Hybrid / Consulting |
| [MISTAKES] | Common mistakes or misconceptions the paper addresses | Hybrid / Consulting |
| [REPLDIFF] | Software, packages, complexity as a working example | Hybrid / Consulting |
| [LIMITS] | Structural assumptions authors acknowledge; where approach breaks down | Hybrid / Consulting |
| [WARN] | Practitioner judgment added by reader -- fragility, generalizability, sample quirks | Hybrid / Consulting |
| [TRANSFER] | Analogous applications inferred from method and research design | Hybrid / Consulting |
| [CLIENT] | Plain-language descriptions; intuitive framings of complex ideas | Hybrid / Consulting |
| [EFFECTSIZE] | Effect size as reported by authors; how authors interpret it -- no cross-field judgment | Hybrid / Consulting |
| [DESIGN] | Pre-analysis structure AND post-hoc design features (randomization, matching, synthetic control, etc.) | Hybrid / Consulting |
| [DECISIONS] | Analytical choices explicitly justified by authors | Hybrid / Consulting |
| [DECISIONS:SILENT] | Analytical choices made without comment or justification | Hybrid / Consulting |
| [FLAG] | Obvious methodological concerns -- use sparingly, only for clear issues (e.g. multiple tests, no correction) | Hybrid / Consulting |

---

## Visual & Reference Flags

| Tag | Captures | Usage note |
|---|---|---|
| [FIG] | Figure worth referencing -- always include description | Append [SLIDE] inline if slide/teaching worthy |
| [TABLE] | Table worth referencing -- always include description | Append [SLIDE] inline if slide/teaching worthy |
| [SLIDE] | Flagged as slide or teaching-ready content | Never standalone -- always follows [FIG] or [TABLE] |
| [QUOTE] | Notable quotation worth preserving verbatim | Include page reference always |

---

## Structural / Meta Tags

| Tag | Captures | Behavior |
|---|---|---|
| [DUP?] | Possible duplicate -- flagged during reading for end-of-paper review | Resolved during deduplication pass |
| [REVISIT] | Unclear, incomplete, or worth re-reading | Can trigger re-read with optional focus |
| [XREF] | Cross-reference potential to another paper in the readings folder | Format as: [XREF] similar [METHOD] to jones_2021 |

---

## Tag Combination Rules

- Multiple tags on a single entry are allowed when genuinely warranted
- [SLIDE] always appears inline after [FIG] or [TABLE] -- never as a standalone entry
- [DECISIONS] and [DECISIONS:SILENT] are mutually exclusive on the same entry
- [WARN] is always the reader's voice; [LIMITS] is always the paper's voice
- [FLAG] should be used sparingly -- if uncertain, use [WARN] instead
- [EFFECTSIZE] records what the paper reports -- cross-field interpretation belongs in [WARN] if needed
- [DESIGN] covers both pre-analysis and post-hoc design features -- do not split between entries

---

## Reading Profiles

| Profile | Academic Dimensions | Practitioner Extensions |
|---|---|---|
| Personal | All 8 | None |
| Consulting | All 8 | All active extensions where content exists |
| Hybrid (default) | All 8 | All active extensions where content exists, clearly marked |

---

## Quick Reference -- All Tags

| Tag | One-line description |
|---|---|
| [RQ] | Research question and motivation |
| [AUD] | Target audience / sub-community |
| [METHOD] | Overall strategy for answering the research question |
| [DATA] | Source, unit of observation, sample size, time period |
| [STATS] | Techniques applied to the data |
| [FINDINGS] | Main results, key estimates, standard errors |
| [CONTRIB] | What is learned that wasn't known before |
| [REPLICATION] | Public data, archive, appendix, URLs |
| [TEACH] | Teachability -- core intuition, appropriate level |
| [MISTAKES] | Common mistakes or misconceptions the paper addresses |
| [REPLDIFF] | Software, packages, complexity as a working example |
| [LIMITS] | Structural assumptions authors acknowledge; where approach breaks down |
| [WARN] | Practitioner judgment added by reader -- fragility, generalizability, sample quirks |
| [TRANSFER] | Analogous applications inferred from method and research design |
| [CLIENT] | Plain-language descriptions; intuitive framings of complex ideas |
| [EFFECTSIZE] | Effect size as reported by authors; how authors interpret it -- no cross-field judgment |
| [DESIGN] | Pre-analysis structure AND post-hoc design features |
| [DECISIONS] | Analytical choices explicitly justified by authors |
| [DECISIONS:SILENT] | Analytical choices made without comment or justification |
| [FLAG] | Obvious methodological concerns -- use sparingly |
| [FIG] | Figure worth referencing -- always include description |
| [TABLE] | Table worth referencing -- always include description |
| [SLIDE] | Flagged as slide or teaching-ready content -- never standalone |
| [QUOTE] | Notable quotation worth preserving verbatim |
| [DUP?] | Possible duplicate -- flagged for end-of-paper review |
| [REVISIT] | Unclear, incomplete, or worth re-reading |
| [XREF] | Cross-reference potential to another paper in the readings folder |
