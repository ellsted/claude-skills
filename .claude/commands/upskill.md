# /upskill

A structured, multi-phase learning system for mastering statistical and data science concepts. Coordinates two specialist personas (Statistician and Coder) across three gated phases.

## Before starting
1. Load the full spec from `upskill/spec.md`
2. Load persona files:
   - `upskill/statistician_persona.md` (Phase 0, 1, and 3)
   - `upskill/coder_persona.md` (Phase 2)

## Usage
```
/upskill <topic>                        # General mode
/upskill <topic> from <paper/source>    # Paper-Specific mode
/re-upskill                             # Reweighted retry using prior final_assessment.md
```

Then follow the phases defined in `upskill/spec.md` exactly.
