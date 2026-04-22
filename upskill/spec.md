# /upskill Skill Specification

## Overview

The `/upskill` skill is a structured, multi-phase learning system that guides a user through mastering a statistical or data science concept. It operates across defined phases, modes, and gates, coordinating two specialist personas: the **Statistician** and the **Coder**. This document defines the full operational architecture of the skill.

---

## Modes

### Mode 1: General Mode
- Activated when the user invokes `/upskill <topic>` without specifying a paper or external source.
- The skill selects canonical textbook material, standard definitions, and established methodology for the topic.
- Reading plan tiers are drawn from a curated hierarchy of sources appropriate to the topic.
- Exercises and quizzes are generated from standard problem banks relevant to the topic.

### Mode 2: Paper-Specific Mode
- Activated when the user invokes `/upskill <topic> from <paper/source>`.
- The skill anchors all phases to the specific paper or source provided.
- Reading plan tiers reference sections, theorems, and notation from the paper.
- Quiz questions and exercises are parameterized using the paper's datasets, notation, and claims.
- The re-upskill protocol reweights toward paper-specific gaps.

---

## Phases

### Phase 0: Intake and Calibration
**Trigger:** User invokes `/upskill`.
**Actions:**
- Collect topic, any source/paper reference, and user-declared experience level (novice / intermediate / advanced).
- If experience level is not declared, run a 3-question diagnostic (authored by the Statistician) to calibrate starting LOU.
- Select mode (General or Paper-Specific).
- Generate the reading plan.
- Write initial metadata block to `final_assessment.md`.

**Gate:** None. Proceed automatically to Phase 1 after intake.

---

### Phase 1: Conceptual Foundations (Statistician-Led)
**Trigger:** Completion of Phase 0.
**Persona:** Statistician
**Actions:**
- Deliver conceptual exposition across LOUs 1-3 (Define, Describe, Explain).
- Present the reading plan for Tier 1 and Tier 2 materials.
- Administer the Phase 1 Quiz (14 questions, Statistician-authored).
- Grade the quiz using the Statistician grading protocol.
- Write the Phase 1 grade report to `final_assessment.md`.

**Gate: Phase 1 Gate**
- Pass condition: Score >= 75% overall AND no LOU entirely failed (defined as 0/2 on any LOU pair).
- Fail condition: Score < 75% OR any LOU entirely failed.
- On fail: Remediation loop is triggered. The Statistician identifies the failed LOUs, re-teaches targeted content, and re-administers a 6-question focused quiz covering only failed LOUs.
- Remediation may be triggered at most twice before the skill flags a deep gap and recommends prerequisite study.
- Gate verdict language is authored by the Statistician.
- On pass: Proceed to Phase 2.

---

### Phase 2: Implementation (Coder-Led)
**Trigger:** Phase 1 Gate pass.
**Persona:** Coder
**Actions:**
- Deliver implementation exposition tied to the conceptual LOUs mastered in Phase 1.
- Present the reading plan for Tier 3 (implementation) materials.
- Administer the exercise ladder (5 exercises, escalating difficulty).
- Grade each exercise using the Coder grading protocol.
- Write the Phase 2 grade report to `final_assessment.md`.
- Track hint usage per exercise.

**Exercise Ladder:**
| Exercise | Difficulty | Focus |
|----------|-----------|-------|
| 1 | Introductory | Direct API call, defaults only |
| 2 | Introductory-Intermediate | Modify one parameter |
| 3 | Intermediate | Multi-step pipeline |
| 4 | Intermediate-Advanced | Custom function wrapping standard tool |
| 5 | Advanced | Full implementation with diagnostics and output interpretation |

**Gate: Phase 2 Gate**
- Pass condition: Average exercise score >= 70% AND Exercise 5 score >= 60%.
- Fail condition: Average < 70% OR Exercise 5 < 60%.
- On fail: Coder identifies weakest exercises, re-administers targeted variants (max 2 retry attempts).
- On pass: Proceed to Phase 3.

---

### Phase 3: Applied Research Design (Statistician-Led)
**Trigger:** Phase 2 Gate pass.
**Persona:** Statistician
**Actions:**
- Present a research design scenario drawn from the scenario type bank.
- User produces a written research design response.
- Statistician grades using the Phase 3 rubric (see statistician_persona.md).
- Write the Phase 3 grade report to `final_assessment.md`.
- Author the final summary and recommendations section of `final_assessment.md`.

**Gate: Phase 3 Gate**
- Pass condition: Phase 3 rubric score >= 70%.
- Fail condition: Score < 70%.
- On fail: Statistician provides targeted feedback and user may revise once.
- On pass: Skill concludes. Final `final_assessment.md` is sealed.

---

## Phase Flow Summary

```
Phase 0 (Intake)
     |
Phase 1 (Conceptual, Statistician)
     |--- [Gate 1 Fail] --> Remediation --> Retry --> Gate 1
     |--- [Gate 1 Pass] -->
Phase 2 (Implementation, Coder)
     |--- [Gate 2 Fail] --> Retry Exercises --> Gate 2
     |--- [Gate 2 Pass] -->
Phase 3 (Research Design, Statistician)
     |--- [Gate 3 Fail] --> Revision --> Gate 3
     |--- [Gate 3 Pass] --> Seal final_assessment.md
```

---

## Levels of Understanding (LOU) Structure

The skill tracks understanding across 6 LOUs:

| LOU | Label | Description |
|-----|-------|-------------|
| 1 | Define | Can state the definition precisely |
| 2 | Describe | Can describe properties and behavior |
| 3 | Explain | Can explain why/how it works |
| 4 | Apply | Can apply in a standard context |
| 5 | Analyze | Can analyze a result or output critically |
| 6 | Synthesize/Evaluate | Can design or evaluate an approach |

Phase 1 covers LOUs 1-3. Phase 2 covers LOUs 4-5. Phase 3 covers LOU 6.
Each quiz question is tagged to an LOU. Each exercise is tagged to LOUs 4-5.

---

## Reading Plan Tiers

### Tier 1: Foundational
- Textbook chapters or canonical definitions.
- Assigned at Phase 0 for pre-reading before Phase 1.
- In Paper-Specific mode: introductory sections and background of the paper.

### Tier 2: Conceptual Depth
- Survey articles, review papers, or extended textbook treatments.
- Assigned during Phase 1.
- In Paper-Specific mode: core methodology sections of the paper.

### Tier 3: Implementation
- Official library documentation, reproducible notebooks, reference implementations.
- Assigned during Phase 2.
- In Paper-Specific mode: any code supplements or reproducibility materials from the paper.

---

## Quiz Composition Rules

- The Phase 1 Quiz contains exactly 14 questions.
- Questions are distributed across LOUs 1-3 as follows:
  - LOU 1 (Define): 4 questions
  - LOU 2 (Describe): 4 questions
  - LOU 3 (Explain): 6 questions
- Question types must include at least 4 of the 7 available types (see statistician_persona.md).
- No two consecutive questions may be the same type.
- In Paper-Specific mode, at least 6 of 14 questions must be anchored to the specific paper.
- All questions are authored by the Statistician.

---

## Exercise Ladder Rules

- Exactly 5 exercises per Phase 2 session.
- Each exercise is tagged to at least one LOU (4 or 5).
- Exercise 1 and 2 are tagged LOU 4. Exercise 3 is tagged LOU 4-5. Exercises 4 and 5 are tagged LOU 5.
- In Paper-Specific mode, at least 3 of 5 exercises must use the paper's dataset, notation, or model.
- Each exercise must include: problem statement, input specification, expected output format, and grading criteria.
- The Coder provides exactly one implementation note per exercise regardless of correctness.

---

## Scenario Type Bank (Phase 3)

The Statistician selects one scenario type for Phase 3 from the following bank:

| Type | Description |
|------|-------------|
| A | Design a study using the target method from scratch |
| B | Critique and correct a flawed research design |
| C | Extend an existing analysis with a new methodological question |
| D | Compare two methodological approaches for a given problem |
| E | Interpret and evaluate ambiguous or conflicting results |
| F | Reproduce and diagnose a published result (Paper-Specific mode only) |

In Paper-Specific mode, Type F is preferred. Types B and D are prioritized for General mode.

---

## Hint System

- The user may request a hint during any Phase 2 exercise.
- Each hint costs 5 points from the maximum achievable score for that exercise.
- Maximum 2 hints per exercise.
- Hint usage is logged in `final_assessment.md` by the Coder.
- Hints provide directional guidance only; they do not provide code solutions.
- In Phase 1, hints are not available. The Statistician may clarify question wording if ambiguity is raised.

---

## Persona Invocation Schedule

| Phase | Persona | Role |
|-------|---------|------|
| Phase 0 | Statistician | Diagnostic questions (if needed) |
| Phase 1 | Statistician | Exposition, quiz authoring, grading, gating |
| Phase 2 | Coder | Implementation exposition, exercises, grading, gating |
| Phase 3 | Statistician | Scenario authoring, rubric grading, final summary |

Personas are never active simultaneously. Transitions are explicit and announced to the user.

---

## Re-Upskill Protocol

Triggered when the user invokes `/re-upskill` after a completed session or when a gate fails more than twice.

### Reweighting Logic
- Retrieve `final_assessment.md` from the prior session.
- Identify all LOUs with scores below threshold (< 75% for conceptual, < 70% for implementation).
- Generate a reweighted session plan:
  - Phase 1 quiz allocates extra questions to failed LOUs (up to +3 questions on failed LOUs, total still capped at 14 by reducing questions from passed LOUs).
  - Phase 2 exercises prioritize exercise types corresponding to failed implementation LOUs.
  - Phase 3 scenario type is selected to specifically target the weakest synthesis area.
- In Paper-Specific mode, re-upskill anchors to the same paper unless user specifies otherwise.
- The reweighted plan is written to a new `final_assessment.md` with a link/reference to the prior session record.

---

## /review Forward Design

The `/review` command is a lightweight refresh skill that activates after a successful `/upskill` session.

### Trigger
- User invokes `/review <topic>` or `/review` (which defaults to the most recently upskilled topic).

### Behavior
- Reads `final_assessment.md` from the completed upskill session.
- Generates a 6-question spaced-repetition quiz targeting the LOUs that scored lowest in the original session.
- Includes 1 implementation micro-exercise (Coder-led, Exercise Ladder level 2-3 only).
- No gates in `/review`. Feedback is formative only.
- Statistician authors the quiz. Coder authors the micro-exercise.
- Results are appended to `final_assessment.md` under a `review_sessions` block.

### Review Cadence Recommendations
- Written to `final_assessment.md` by the Statistician at session close.
- Recommended intervals: 1 day, 3 days, 7 days, 14 days (standard spaced repetition).

---

## final_assessment.md Schema Reference

The full schema for `final_assessment.md` is defined and owned by the Statistician. See `statistician_persona.md` for the complete schema specification. The Coder has read access to the `implementation_results` block only.

---

## Operational Directives

- All phase transitions must be announced explicitly to the user.
- Gate verdicts must state: verdict (pass/fail), score achieved, threshold, and next action.
- The skill never skips a phase or gate without explicit user override and a logged warning in `final_assessment.md`.
- All content is adapted to the user's declared or diagnosed experience level.
- The skill maintains a consistent, directive, pedagogically rigorous tone throughout.
- Personas speak in their own voice (see persona files) but the skill's scaffolding language is neutral and structured.
