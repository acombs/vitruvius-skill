# Rubrics — <demo name>

> Stage 3 artifact. Written BEFORE any code, one block per phase. Format and
> pass condition defined in references/rubric-format.md. Tailor weights/floors
> to what each phase is about (money-moment phase weights Wow heaviest). For
> non-UI phases use the non-UI variant table from rubric-format.md.

## Phase 1 — <name>

### Must-pass gates  (binary; ALL must pass)
- [ ] G1. This phase's narrative beats run clean end to end — Evidence: walkthrough screenshots per beat
- [ ] G2. Zero placeholder content in any reachable state    — Evidence: sweep of all reachable screens
- [ ] G3. No console errors on the demo path                 — Evidence: console log of full walkthrough
- [ ] G4. Correct at presentation resolution <res>           — Evidence: screenshots at that resolution
<!-- If spec.md names a design spec AND this phase has UI, add: -->
- [ ] GD. UI conforms to <DESIGN.md> (tokens, spacing, components, states) — Evidence: side-by-side with the spec
<!-- plus phase-specific gates, e.g. "the anomaly appears in beat 3's chart" -->

### Quality dimensions  (1–5, weighted, with floors)  <!-- swap for non-UI table if no screen -->
| Dim | Dimension              | Weight | Floor | Evidence to gather |
|-----|------------------------|--------|-------|--------------------|
| Q1  | Wow / first impression | 0.30   | 3     | 5-second screenshot test vs north-star |
| Q2  | Narrative flow         | 0.20   | 3     | timed walkthrough of the beats |
| Q3  | Data credibility       | 0.20   | 3     | domain read of visible names/numbers/dates |
| Q4  | Polish & taste         | 0.20   | 3     | spacing/type/color sweep vs north-star |
| Q5  | Perceived performance  | 0.10   | 3     | interaction timings |

### Panel question
"Would <this audience>, holding it to <taste north-star>, lean forward? Where
does it fall flat?"

### Holistic veto
Looks like the future, or a template with the labels changed? Every box ticked
but the room would shrug → FAIL.

### Pass condition
PASS iff: all gates pass AND all prior phases' gates + beats still pass
(demo-path regression) AND weighted score ≥ 3.5 AND no dimension below floor
AND holistic veto not invoked.
(Score within ~0.2 of threshold = tie → resolve on veto/panel or ask the human.)

<!-- repeat the block for each phase -->
