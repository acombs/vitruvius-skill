# Rubrics — <project name>

> Stage 3 artifact. Written BEFORE any code, one block per phase. Format and pass
> condition defined in references/rubric-format.md. Tailor weights/floors to what
> each phase is actually about. For non-UI phases use the capability-slice
> dimension table from rubric-format.md instead of the UI one below.

## Phase 1 — <name>

### Must-pass gates  (binary; ALL must pass)
- [ ] G1. Tests pass green                                   — Evidence: test run output
- [ ] G2. No type/lint errors                                — Evidence: linter/compiler output
- [ ] G3. No secrets in code, config, or logs                — Evidence: sweep of committed files
- [ ] G4. Error/empty/loading states handled on this phase's paths — Evidence: screenshots/output per state
- [ ] G5. <phase-specific non-negotiable>                    — Evidence: <what proves it>
<!-- If spec.md names a design spec AND this phase has UI, add: -->
- [ ] GD. UI conforms to <DESIGN.md> (tokens, spacing, components, states)  — Evidence: side-by-side with the spec

### Quality dimensions  (1–5, weighted, with floors)  <!-- UI variant; swap for capability-slice table if no UI -->
| Dim | Dimension       | Weight | Floor | Evidence to gather |
|-----|-----------------|--------|-------|--------------------|
| Q1  | Usability       | 0.25   | 3     |  |
| Q2  | Taste / visual  | 0.20   | 3     |  |
| Q3  | Robustness      | 0.20   | 3     |  |
| Q4  | Performance     | 0.10   | 2     |  |
| Q5  | Accessibility   | 0.10   | 2     |  |
| Q6  | Maintainability | 0.15   | 3     |  |

### Panel question
"Would <taste north-star> rate this highly? Where does it fall down?"

### Holistic veto
Does this feel like something a great team shipped? Passes every box but a great
designer would wince → FAIL.

### Pass condition
PASS iff: all gates pass AND all prior phases' gates still pass (regression) AND
weighted score ≥ 3.5 AND no dimension below floor AND holistic veto not invoked.
(Score within ~0.2 of threshold = tie → resolve on veto/panel or ask the human.)

<!-- repeat the block for each phase -->
