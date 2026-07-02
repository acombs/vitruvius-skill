# Rubric format (hybrid) and pass condition

The rubric is what makes the gate objective instead of vibes. It's **hybrid**:
binary must-pass gates for non-negotiables, plus weighted numeric scores for
the softer quality dimensions. Use this exact structure per phase so the
pass/fail decision is mechanical and a fresh critic reads it the same way you
wrote it.

## Structure (one block per phase, in `rubric.md`)

```markdown
## Phase <n> — <phase name>

### Must-pass gates  (binary; ALL must pass or the phase fails)
- [ ] G1. This phase's narrative beats run clean end to end — Evidence: recorded walkthrough / screenshots per beat
- [ ] G2. Zero placeholder content in any reachable state       — Evidence: sweep of all reachable screens
- [ ] G3. No console errors on the demo path                    — Evidence: console log of full walkthrough
- [ ] G4. Correct at presentation resolution <e.g. 1920×1080>   — Evidence: screenshots at that resolution
- [ ] G5. <phase-specific non-negotiable>                       — Evidence: <…>

### Quality dimensions  (score 1–5; weighted; each has a floor)
| Dim | Dimension               | Weight | Floor | Evidence to gather                          |
|-----|-------------------------|--------|-------|---------------------------------------------|
| Q1  | Wow / first impression  | 0.30   | 3     | 5-second screenshot test vs north-star       |
| Q2  | Narrative flow          | 0.20   | 3     | timed walkthrough of the beats               |
| Q3  | Data credibility        | 0.20   | 3     | domain read of visible names/numbers/dates   |
| Q4  | Polish & taste          | 0.20   | 3     | spacing/type/color sweep vs north-star       |
| Q5  | Perceived performance   | 0.10   | 3     | interaction timings; anything > ~300ms noted |

### Panel question
"Would <this audience>, holding it to <taste north-star>, lean forward? Where
does it fall flat?"
<critic answers in prose at the gate>

### Holistic veto
Does this look and feel like the future, or like a template with the labels
changed? Every box ticked but the room would shrug → FAIL. <critic justifies
invoking or not>

### Pass condition
PASS iff:
  • every must-pass gate passes, AND
  • every prior phase's gates + narrative beats still pass (demo-path regression), AND
  • weighted quality score ≥ <threshold, e.g. 3.5 / 5>, AND
  • no quality dimension is below its floor, AND
  • the holistic veto is not invoked.

BORDERLINE: a weighted score within ~0.2 of the threshold is a tie, not a
pass — decide on the holistic veto and panel answer, or hand it to the human.
```

## Notes on each part

- **Must-pass gates** are the things where "mostly works" equals broken on
  stage. Keep them few and sharp. G1–G4 above are the standard demo set; add
  phase-specific gates ("the anomaly is visible in beat 3's chart").
- **Design-spec conformance is a gate, not a score.** If `spec.md` names a
  design spec (`DESIGN.md`, brand tokens, a component library), every UI phase
  carries a binary conformance gate with side-by-side evidence. Taste asks "is
  it good"; conformance asks "does it match what we were handed". A beautiful
  screen that ignores the client's brand system still fails the gate.
- **Weights** express what *this* phase is about. The money-moment phase might
  weight Wow 0.40; the off-script resilience pass might swap Wow down and add
  an On-path robustness dimension at 0.30. Weights sum to 1.0.
- **Floors** stop dimension-trading: a gorgeous phase with fabricated-looking
  data should fail. Note Q5's floor is 3, not 2 — in a demo, jank *is* failure;
  perceived speed is part of the illusion.
- **Evidence** is mandatory per item. Can't name evidence → the item is
  unmeasurable → rewrite it. At the gate, a score citing no gathered evidence
  is a low-confidence score.
- **The 5-second test (Q1):** put the beat's opening screenshot next to the
  north-star's. Would a stranger say they belong to the same tier? That's the
  wow score's anchor.
- **Panel question** forces comparison to a named bar and a named audience —
  "falls short of Linear's density here and here" beats "looks fine". Graded
  critique: how close, and where's the gap.
- **Holistic veto** is binary — a kill-switch, not a grade. The panel answer
  can say "8/10, weak in two beats" and still pass; the veto fires only for
  "every box ticked and the room would still shrug".

## Non-UI phase variant (data pipeline / API-backing / CLI demo)

Occasionally a demo phase has no screen — a data-seeding phase, a mock-API
layer, a CLI being demoed. Keep the same structure; swap dimensions:

| Dim | Dimension                  | Weight | Floor | Evidence to gather                       |
|-----|----------------------------|--------|-------|-------------------------------------------|
| Q1  | Output legibility / drama  | 0.30   | 3     | real invocations; what the audience sees   |
| Q2  | Data credibility           | 0.25   | 3     | domain read of generated data              |
| Q3  | On-path robustness         | 0.25   | 3     | scripted inputs + likely off-script inputs |
| Q4  | Perceived performance      | 0.20   | 3     | timings at demo data volume                |

The panel question becomes *"would this audience find this output legible and
impressive as it scrolls past?"*

## Worked scoring example

Phase 2, threshold 3.5, weights as in the UI table. Critic gathers evidence
and scores: Q1=4, Q2=3, Q3=4, Q4=4, Q5=3. All gates pass. Veto not invoked.

Weighted = 4(.30)+3(.20)+4(.20)+4(.20)+3(.10) = 1.20+0.60+0.80+0.80+0.30 =
**3.70 ≥ 3.5** → within no floor violations → **PASS.**

Counter-example: same scores but Q3 (Data credibility) = 2 — the chart's
revenue numbers are off by two orders of magnitude for the industry. Floor for
Q3 is 3 → **FAIL on floor**, even though the weighted total barely moves. The
floor catches the beautiful-but-unbelievable trap.

## Low-confidence scores

When wow/credibility/flow is scored from code rather than a running artifact,
mark it `(low-confidence)` in the eval. A low-confidence score on Q1–Q3 must
not be used to *assert* a pass — surface it: "I can't run this to judge the
first impression; here's my code-only read — want to verify before I gate it?"
