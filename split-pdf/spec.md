# split_pdf_spec.md
# Spec for Claude Code: /split-pdf Slash Command

## Covering Note

This document is a spec for a Claude Code slash command called `/split-pdf`.

**What Python scaffolding handles automatically:**
- PDF acquisition (download or local path verification)
- Splitting the PDF into 4-page chunks using PyPDF2
- Folder routing and sub-folder management under `Readings/`
- Duplicate filename detection
- `notes.md` initialization with YAML front matter

**What requires a language model (conversational handoff to Claude):**
The following steps cannot and should not be automated with Python. Each requires
a language model to read the content of PDF chunks and reason about it:
- Step 5: Reading Loop -- reading chunks and writing structured notes
- Step 6: Note Format -- tagging entries using tag_reference.md vocabulary
- Step 7: Deduplication Pass -- identifying and resolving redundant entries
- Step 8: Synthesis Block -- writing the synthesis section at the top of notes.md

These steps must be stubbed out as clearly marked placeholders that trigger a
conversational handoff back to Claude, with the relevant chunk(s) loaded as context.

**Required dependency:**
This spec includes a required dependency file: `tag_reference.md`
It must be saved to `Readings/tag_reference.md` before the skill is first run.
The skill will halt and notify the user if this file is not found.

NOTE: 

All PDF storage, folder creation, splitting, and tag_reference.md 
should be rooted at $READINGS_ROOT.
Set READINGS_ROOT in your shell config before first use.
See .env.example in the repo root.

---

## Skill Prompt -- /split-pdf

## Preamble & Dependencies

You are executing the /split-pdf skill. Before doing anything else:

1. Locate `tag_reference.md` at `$READINGS_ROOT/tag_reference.md`
   - If not found: STOP and notify the user before proceeding
   - If found: load it silently -- do not summarize it to the user
2. Confirm Python environment has PyPDF2 available
   - If not: notify user and stop

## Step 1 -- Acquire the PDF

Accept one of:
- A URL (required for remote files -- do not attempt to find URLs yourself)
- A local file path

If URL:
- Attempt download
- If paywalled or access denied: notify user with message
  "This paper appears to be behind a paywall. Please provide an
   open-access URL or a local file path." Then stop.
- Save to target folder (determined in Step 2)

If local path:
- Verify file exists
- If not found: notify user and stop

Duplicate check:
- If a file with the same name already exists in the target folder:
  notify user -- "A file named [name] already exists at [path].
  Is this a duplicate, or should I proceed with a new copy?"
- Wait for user confirmation before proceeding

## Step 2 -- Folder Routing

Ask: "Where should I save this paper?"

- If user specifies a sub-folder: use it
- If user does not specify:
  - List all existing sub-folders in Readings/
  - Suggest 2-3 candidates based on paper topic, author, year,
    and existing folder names
  - Ask user to pick one, use an existing folder, or create a new one
  - If creating new: ask for folder name

Target structure:
$READINGS_ROOT/
└── [subfolder]/
    ├── [name]_[year].pdf          # original -- NEVER deleted
    └── split_[name]_[year]/
        ├── [name]_[year]_pp1-4.pdf
        ├── [name]_[year]_pp5-8.pdf
        └── notes.md

## Step 3 -- Reading Profile

Ask: "Which reading profile should I use?
  [H] Hybrid (default) -- full academic read + practitioner extensions
  [P] Personal -- academic dimensions only
  [C] Consulting -- academic dimensions + practitioner extensions emphasized"

Default to Hybrid if user presses enter without responding.

Store profile selection in YAML front matter of notes.md.

## Step 4 -- Split the PDF

Using PyPDF2:
- Split into 4-page chunks
- If final chunk is fewer than 4 pages: include as-is, do not pad
- Name chunks as: [name]_[year]_pp[X]-[XX].pdf
  where X and XX are the first and last page numbers of that chunk
- Save all chunks to split_[name]_[year]/
- Initialize notes.md in the same folder (see structure below)
- Never delete or modify the original PDF

## Step 5 -- Reading Loop

WARNING: LANGUAGE MODEL STEP -- DO NOT AUTOMATE WITH PYTHON

Read chunks in batches of up to 3 (maximum 12 pages):
- If paper has fewer than 12 pages: read in a single batch
- If paper does not divide evenly: final batch may be 1 or 2 chunks

For each batch:
  1. Read all chunks in the batch carefully before writing any notes
  2. Identify the paper's own section headers within those pages
  3. Write notes under those headers (see Note Format below)
  4. After writing: display --
     "Finished reviewing pp. X-Y. Ready to continue? (y/n)"
  5. Wait for user response before proceeding to next batch

Re-read trigger:
  - User may say "re-read pp. X-Y" at any pause point
  - User may optionally specify a focus: "re-read pp. X-Y,
    focus on measurement choices"
  - Re-read output is APPENDED under the original section as:
    ### Re-read [pp. X-Y] -- Focus: [stated purpose or "general"]
  - Never replaces original notes

## Step 6 -- Note Format

WARNING: LANGUAGE MODEL STEP -- DO NOT AUTOMATE WITH PYTHON

All notes written under the paper's own section headers.
Each entry tagged with vocabulary from tag_reference.md.
Page references included on every entry.

Format:

## [Paper Section Header] [pp. X-Y]
- [TAG] Note content here [pp. X]
- [TAG] Note content here [pp. X-Y]
- [DECISIONS:SILENT] Outliers removed from sample without
  explanation [pp. 6]
- [FIG] Figure 2 -- marginal effects plot, X axis = GDP per capita,
  notable result clearly visible -- [SLIDE] [pp. 14]

Rules:
- Use paper's exact section headers, not generic labels
- Multiple tags on one entry are allowed when genuinely warranted
- [SLIDE] always appears inline after [FIG] or [TABLE] when flagged,
  never as a standalone entry
- [FLAG] used sparingly -- only for clear, obvious concerns
  (e.g. 47 tests run, no multiple comparison correction noted)
- [DECISIONS] for choices the authors justify explicitly
- [DECISIONS:SILENT] for choices made without comment
- Practitioner tags ([TEACH], [CLIENT], [WARN], etc.) included
  in all Hybrid and Consulting runs; omitted in Personal runs

## Step 7 -- Deduplication Pass

WARNING: LANGUAGE MODEL STEP -- DO NOT AUTOMATE WITH PYTHON

After the final batch is confirmed by user:

1. Review all notes.md entries for duplicates and redundancies
2. Produce a numbered list:

   Proposed cuts/merges for your review:

   1. POSSIBLE DUPLICATE -- [FINDINGS]
      A: "Main finding: b = 0.43 (SE = 0.12) [pp. 3]"
         (Introduction)
      B: "OLS result: b = 0.43, SE = 0.12, p < 0.01 [pp. 16]"
         (Results)
      -> Suggest: keep B (more complete), note in synthesis
        that finding is flagged in intro

   2. ...

3. Wait for user to approve, modify, or reject each proposed cut
4. Apply only approved changes
5. Do not proceed to synthesis until deduplication is complete

## Step 8 -- Synthesis Block

WARNING: LANGUAGE MODEL STEP -- DO NOT AUTOMATE WITH PYTHON

After deduplication is approved, write the synthesis block
at the TOP of notes.md, above all reading notes.

Always include all 8 academic dimensions.
Mark as "Not addressed in paper" if a dimension is absent --
do not omit the header.

Include practitioner extensions only if profile = Hybrid or Consulting.
Include only extensions where content was actually extracted --
omit empty practitioner sections.

Replication section rendered as table always.

## Final notes.md Structure

---
title:
authors:
year:
journal:
doi:
url:
date_read:
profile: hybrid | personal | consulting
---

# [Synthesis] name_year

## Research Question
## Audience
## Method
## Data
## Statistical Methods
## Findings
## Contributions
## Replication

| Replication link | URL | Data availability |
|-----------------|-----|-------------------|
| Y/N | | |

---
<!-- Practitioner Synthesis -- Hybrid/Consulting only -->

## Teachability
## Common Mistakes Addressed
## Replication Difficulty
## Assumptions & Limits
## Practitioner Warnings
## Analogous Applications
## Client Communication Notes
## Effect Size Notes
## Design Notes
## Analytical Decisions Log

---

# [Reading Notes] name_year

## [Paper Section Header] [pp. X-Y]
- [TAG] content [pp. X]

## [Paper Section Header] [pp. X-Y]
- [TAG] content [pp. X-Y]

## Error Handling Summary

Stop and notify user if:
- tag_reference.md not found at $READINGS_ROOT/
- READINGS_ROOT environment variable is not set
- PyPDF2 not available
- PDF not found at local path
- Paper is paywalled
- Duplicate filename detected (await user decision)

Continue with inline note if:
- Section header is ambiguous
- A dimension is not addressed in the paper
- Chunk count does not divide evenly into batches of 3

---

## tag_reference.md -- Save to Readings/tag_reference.md

```
# Tag Reference -- /split-pdf Skill
# Location: Readings/tag_reference.md
# Version: 1.0
# Last updated: 2026-03-26
#
# This file is a required dependency of the /split-pdf skill.
# The skill loads it automatically at the start of every run.
# Do not rename or move this file.
# A human-readable copy is maintained here for reference.

---

## Academic Dimensions (Core 8)

| Tag | Captures |
|-----|----------|
| `[RQ]` | Research question and motivation |
| `[AUD]` | Target audience / sub-community |
| `[METHOD]` | Overall strategy for answering the research question |
| `[DATA]` | Source, unit of observation, sample, time period |
| `[STATS]` | Techniques applied to the data |
| `[FINDINGS]` | Main results, key estimates, standard errors |
| `[CONTRIB]` | What is learned that wasn't known before |
| `[REPLICATION]` | Public data, archive, appendix -- rendered as table |

---

## Practitioner Extensions

| Tag | Captures |
|-----|----------|
| `[TEACH]` | Teachability -- core intuition, appropriate level |
| `[MISTAKES]` | Common mistakes or misconceptions the paper addresses |
| `[REPLDIFF]` | Software, packages, complexity as a working example |
| `[LIMITS]` | Structural assumptions authors acknowledge; where it breaks down |
| `[WARN]` | Your practitioner judgment -- fragility, generalizability, sample quirks |
| `[TRANSFER]` | Analogous applications inferred from method and design |
| `[CLIENT]` | Plain-language descriptions; intuitive framings of complex ideas |
| `[EFFECTSIZE]` | Effect size as reported; how authors interpret it |
| `[DESIGN]` | Pre-analysis structure AND post-hoc design features |
| `[DECISIONS]` | Analytical choices explicitly justified by authors |
| `[DECISIONS:SILENT]` | Analytical choices made without comment or justification |
| `[FLAG]` | Obvious methodological concerns -- e.g. multiple tests, no correction |

---

## Visual & Reference Flags

| Tag | Captures |
|-----|----------|
| `[FIG]` | Figure worth referencing -- with description |
| `[TABLE]` | Table worth referencing -- with description |
| `[SLIDE]` | Flagged as slide/teaching-ready -- always inline after [FIG] or [TABLE] |
| `[QUOTE]` | Notable quotation worth preserving verbatim |

---

## Structural / Meta Tags

| Tag | Captures |
|-----|----------|
| `[DUP?]` | Possible duplicate -- flagged during reading for end review |
| `[REVISIT]` | Unclear, incomplete, or worth re-reading |
| `[XREF]` | Cross-reference potential -- note connection to another paper |

---

## Usage Rules

1. Every extracted note entry must carry at least one tag
2. Multiple tags on one entry are allowed when genuinely warranted
3. `[SLIDE]` always appears inline after `[FIG]` or `[TABLE]` -- never standalone
4. `[DECISIONS]` = author justified it explicitly
   `[DECISIONS:SILENT]` = choice made without comment or justification
5. `[FLAG]` is used sparingly -- only for clear, obvious methodological concerns
6. `[WARN]` is your voice -- practitioner judgment you are adding
   `[LIMITS]` is the paper's voice -- what the authors themselves acknowledge
7. Practitioner extension tags are included in Hybrid and Consulting runs only
8. All entries include page references in the format [pp. X] or [pp. X-Y]
9. `[XREF]` entries should note the connected paper by filename
   e.g. `[XREF] similar DiD design to jones_2021`

---

## Tag Count Summary

- Academic core: 8
- Practitioner extensions: 12
- Visual / reference: 4
- Structural / meta: 3
- Total: 27

---

## Profile Reference

| Profile | Tags active |
|---------|------------|
| Personal | Academic core + Visual/Reference + Structural/Meta |
| Consulting | All tags |
| Hybrid (default) | All tags |
```

---

## Implementation Notes for Claude Code
 - At startup, check that READINGS_ROOT is set as an environment 
  variable. If not: STOP and notify the user with setup instructions 
  before doing anything else.
- Python scaffolding is required for: PDF download, duplicate detection, folder
  routing, PyPDF2 splitting, notes.md initialization, and file naming
- Steps 5 through 8 (Reading Loop, Note Format, Deduplication, Synthesis) require
  a language model -- stub these out as clearly marked placeholders that hand off
  to a conversational Claude session with the relevant chunk(s) loaded as context
- tag_reference.md must be saved to Readings/tag_reference.md before the skill is
  first run -- include a setup check that halts and notifies the user if it is missing
- The original PDF must never be deleted or modified under any circumstances
- notes.md lives inside the split_[name]_[year]/ folder, not in the subfolder root
- All file naming follows the convention: [name]_[year]_pp[X]-[XX].pdf using actual
  page numbers

