# Rubrics — <project name>

> Stage 3 artifact. Written BEFORE any code, one block per phase. Format and pass
> condition defined in references/rubric-format.md. Tailor weights/floors to what
> each phase is actually about.

## Phase 1 — <name>

### Must-pass gates  (binary; ALL must pass)
- [ ] G1. <non-negotiable assertion>  — Evidence: <what proves it>
- [ ] G2. <…>                          — Evidence: <…>

### Quality dimensions  (1–5, weighted, with floors)
| Dim | Dimension      | Weight | Floor | Evidence to gather |
|-----|----------------|--------|-------|--------------------|
| Q1  | Usability      | 0.30   | 3     |  |
| Q2  | Taste / visual | 0.25   | 3     |  |
| Q3  | Robustness     | 0.20   | 3     |  |
| Q4  | Performance    | 0.10   | 2     |  |
| Q5  | Accessibility  | 0.15   | 2     |  |

### Panel question
"Would <taste north-star> rate this highly? Where does it fall down?"

### Holistic veto
Does this feel like something a great team shipped? Passes every box but a great
designer would wince → FAIL.

### Pass condition
PASS iff: all gates pass AND weighted score ≥ 3.5 AND no dimension below floor AND
holistic veto not invoked.

<!-- repeat the block for each phase -->
