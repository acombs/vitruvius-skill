# Phase <n> evaluation — <phase name>

> Stage 5/6 artifact. Critic role, adversarial brief, judged against the
> running artifact and the phase rubric — not the builder's intentions. Append
> a new round each remediation cycle so the score trajectory is visible.

## Round 1
**Evidence gathered:** <screenshots per beat, timings, console logs — list paths>
**Confidence:** live | code-only (low-confidence)  <!-- low-confidence on wow/credibility/flow → surface to human -->

### Must-pass gates
- [ ] G1 — <pass/fail> — <evidence cited>
- [ ] G2 — <pass/fail> — <evidence cited>

### Quality scores
| Dim | Score (1–5) | Evidence / reasoning |
|-----|-------------|----------------------|
| Q1 Wow / first impression |  |  |
| Q2 Narrative flow         |  |  |
| Q3 Data credibility       |  |  |
| Q4 Polish & taste         |  |  |
| Q5 Perceived performance  |  |  |

**Weighted score:** <x.xx>   **Any dimension below floor?** <yes/no>
**Borderline?** <no | within ~0.2 of threshold → tie, resolve on veto/panel or ask human>

### Demo-path regression  (prior phases' gates + beats re-walked)
- [ ] Phase 1 beats + gates still pass — <evidence / which regressed>
- [ ] Phase 2 beats + gates still pass — <…>

### Panel answer
<would this audience lean forward? where it falls flat, specifically>

### Holistic veto
<invoked? justify — "template face" is exactly what this catches>

### Verdict
**PASS / FAIL.**

### Defect checklist (if FAIL)
- [ ] <specific, actionable fix — "beat 3 tooltip shows `undefined` for week 12">
- [ ] <…>

---

## Round 2
**Score delta vs round 1:** <improved / no improvement → early-exit>
<!-- repeat the round structure. ≤3 rounds. No improvement → early-exit. Still
failing at 3 / on early-exit → escalate to human with diagnosis + recommendation
(cut the beat, fake it, change approach, accept with tradeoff named). -->
