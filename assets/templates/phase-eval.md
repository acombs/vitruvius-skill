# Phase <n> evaluation — <phase name>

> Stage 5/6 artifact. Critic role, adversarial brief, judged against the running
> artifact and the phase rubric — not the builder's intentions. Append a new
> round each remediation cycle so the score trajectory is visible.

## Round 1
**Evidence gathered:** <screenshots, logs, test output, CLI invocations — list paths>
**Confidence:** live | code-only (low-confidence)  <!-- low-confidence on taste/usability → surface to human -->

### Must-pass gates
- [ ] G1 — <pass/fail> — <evidence cited>
- [ ] G2 — <pass/fail> — <evidence cited>

### Quality scores
| Dim | Score (1–5) | Evidence / reasoning |
|-----|-------------|----------------------|
| Q1 Usability       |  |  |
| Q2 Taste / visual  |  |  |
| Q3 Robustness      |  |  |
| Q4 Performance     |  |  |
| Q5 Accessibility   |  |  |
| Q6 Maintainability |  |  |

**Weighted score:** <x.xx>   **Any dimension below floor?** <yes/no>
**Borderline?** <no | within ~0.2 of threshold → tie, resolve on veto/panel or ask human>

### Regression check  (prior phases' must-pass gates re-run)
- [ ] Phase 1 gates still pass — <evidence / which regressed>
- [ ] Phase 2 gates still pass — <…>

### Panel answer
<where it falls short of the taste north-star, specifically>

### Holistic veto
<invoked? justify>

### Verdict
**PASS / FAIL.**

### Defect checklist (if FAIL)
- [ ] <specific, actionable fix>
- [ ] <…>

---

## Round 2
**Score delta vs round 1:** <improved / no improvement → early-exit>
<!-- repeat the round structure. ≤3 rounds. No improvement → early-exit. Still
failing at 3 / on early-exit → escalate to human with diagnosis + recommendation. -->
