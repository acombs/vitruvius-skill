# Rubric format (hybrid) and pass condition

The rubric is what makes the gate objective instead of vibes. It's **hybrid**:
binary must-pass gates for non-negotiables, plus weighted numeric scores for the
softer quality dimensions. Use this exact structure for each phase so the
pass/fail decision is mechanical and a fresh critic reads it the same way you
wrote it.

## Structure (one block per phase, in `rubric.md`)

```markdown
## Phase <n> — <phase name>

### Must-pass gates  (binary; ALL must pass or the phase fails)
- [ ] G1. <non-negotiable assertion>  — Evidence: <what proves it>
- [ ] G2. <…>                          — Evidence: <…>

### Quality dimensions  (score 1–5; weighted; each has a floor)
| Dim | Dimension          | Weight | Floor | Evidence to gather                |
|-----|--------------------|--------|-------|-----------------------------------|
| Q1  | Usability          | 0.30   | 3     | screenshots of each core flow     |
| Q2  | Taste / visual     | 0.25   | 3     | screenshots; compare to north-star|
| Q3  | Robustness         | 0.20   | 3     | empty/error/loading states tested |
| Q4  | Performance        | 0.10   | 2     | load time / interaction latency   |
| Q5  | Accessibility      | 0.15   | 2     | a11y check (contrast, kbd, labels)|

### Panel question
"Would <taste north-star> rate this highly? Where does it fall down?"
<critic answers in prose at the gate>

### Holistic veto
Does this feel like something a great team shipped? If it passes every box above
but a great designer would wince, it FAILS. <critic justifies invoking or not>

### Pass condition
PASS iff:
  • every must-pass gate passes, AND
  • every prior phase's must-pass gates still pass (regression), AND
  • weighted quality score ≥ <threshold, e.g. 3.5 / 5>, AND
  • no quality dimension is below its floor, AND
  • the holistic veto is not invoked.

BORDERLINE: if the weighted score is within ~0.2 of the threshold, treat it as a
tie, not a pass — decide on the holistic veto and panel answer, or hand it to the
human. The two-decimal total is not precise enough to settle a near call on its
own.
```

## Notes on each part

- **Must-pass gates** are correctness and non-negotiables — the things where
  "mostly works" is the same as broken. Keep them few and sharp.
- **Weights** express what *this* phase is about. A foundation phase might weight
  Robustness 0.35 and Taste 0.10; a polish phase flips that. Weights sum to 1.0.
- **Floors** prevent trading a great score in one dimension for a terrible one in
  another. A blazing-fast but inaccessible UI should still fail. The floor is the
  "you can't buy your way out of this" line.
- **Evidence** is mandatory per item. If you can't name evidence, the item is
  unmeasurable — rewrite it until you can. At the gate, every score must cite the
  evidence actually gathered; a score with no evidence is a low-confidence score.
- **Panel question** forces a *comparison to a named bar*, which is what turns
  "looks fine" into "falls short of Linear's density here, here, and here". It's a
  graded critique — *how close to the bar, and where the gap is*.
- **Holistic veto** is the anti-gaming backstop, and it's *binary* — a kill-switch,
  not a grade. The panel answer can say "8/10, weak in two places" and still pass;
  the veto fires only for "every box is ticked and it's still bad", and when it
  fires the phase fails outright regardless of the weighted total. Panel = how
  good; veto = is it shippable at all.

## Capability-slice variant (CLI / library / backend / API)

When a phase has no UI to look at, the UI dimensions above don't fit. Keep the
same hybrid structure and pass condition, but swap the quality dimensions for what
"taste" means without a screen — ergonomics, legibility, and durability:

| Dim | Dimension              | Weight | Floor | Evidence to gather                    |
|-----|------------------------|--------|-------|---------------------------------------|
| Q1  | API / CLI ergonomics   | 0.25   | 3     | real invocations; signatures; defaults|
| Q2  | Error & output quality | 0.20   | 3     | error messages on bad input; logs     |
| Q3  | Robustness             | 0.25   | 3     | empty/malformed/huge inputs; failures |
| Q4  | Performance            | 0.15   | 2     | timing on representative input        |
| Q5  | Test coverage / DX     | 0.15   | 2     | tests run green; how easy to extend   |

The panel question becomes *"would <the taste north-star for tools — e.g. the
`ripgrep`/`stripe` CLI bar> rate this API highly?"* and the holistic veto asks
whether a careful library author would be comfortable putting their name on it.

## Worked scoring example

Phase 2 rubric: threshold 3.5, weights as in the table above. Critic gathers
evidence and scores: Q1=4, Q2=3, Q3=4, Q4=5, Q5=3. All gates pass. Holistic not
invoked.

Weighted = 4(.30) + 3(.25) + 4(.20) + 5(.10) + 3(.15) = 1.20+0.75+0.80+0.50+0.45
= **3.70 ≥ 3.5**. No dimension below floor. → **PASS.**

Counter-example: same scores but Q2 (Taste) = 2. Floor for Q2 is 3 → **FAIL on
floor**, even though the weighted total (3.70 − 0.25 = 3.45… still close) might
look survivable. The floor catches the ugly-but-fast trap.

## Low-confidence scores

When taste/usability is scored from code rather than a running artifact (no
preview possible), mark the score `(low-confidence)` in the eval. A low-confidence
score on Q1/Q2 should not be used to *assert* a pass — instead surface it to the
human ("I can't run this to judge usability; here's my code-only read — want to
verify before I gate it?").
