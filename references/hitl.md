# Human-in-the-loop policy

The human is a control surface, not a spectator. Two distinct kinds of pause;
support both, and don't collapse them into one.

## 1. Clarification pauses (anytime, including mid-stage)

Trigger the moment the audience, the narrative, or a requirement is
**genuinely ambiguous** — two reasonable readings would produce different
demos. A wrong assumption made silently in stage 0 compounds through every
phase; the cheapest moment to resolve it is now.

Rules:
- **Batch.** Collect open questions and ask them together. Five separate
  interruptions is worse than one block of five. Exception: a true blocker
  nothing else can proceed without.
- **Don't fabricate to avoid asking.** If you don't know whether the exec
  drives the demo or watches it, ask — don't pick one and bury the assumption.
- **Make it answerable.** Concrete options with a recommendation ("projected
  1080p or the exec's laptop? I'd design for 1080p projected — the references
  are all wide layouts"), not open-ended "what do you want?".
- **Distinguish ambiguity from preference.** Conventional defaults you can
  verify (chart library, matching the repo's formatting) don't need a
  question — decide, state it, move on. Reserve questions for decisions that
  are genuinely the human's: audience, narrative beats, what to fake vs build,
  what to cut under deadline.

## 2. Gate-approval pauses (at boundaries)

Four mandatory checkpoints:

- **(a) After plan + pre-mortem, before any code.** Present the narrative,
  `plan.md`, and `rubric.md`. Highest-leverage checkpoint — changing the
  script here costs a sentence; after three phases it costs days. Always on.
- **(b) At every phase gate.** When a phase passes its rubric, get sign-off
  before the next begins. Governed by **`phase_gate_signoff`** (default
  **on**). When off, passing phases auto-advance; you still pause for (a),
  (c), (d), and clarifications.
- **(c) On escalation.** A phase that can't pass within 3 remediation rounds
  (or early-exits while failing) stops and goes to the human. Always on —
  never silently advance past a failing phase, never loop forever.
- **(d) Dress rehearsal (stage 7).** Present the whole-demo verdict, timing,
  and evidence for sign-off before calling it done. Always on — green phases
  are not a working performance.

**Mid-build spec/narrative change.** A changed requirement or narrative beat
isn't a normal clarification — it can invalidate rubrics already written for
later phases. Update `spec.md`, flag which downstream rubrics it touches, and
re-approve those at the next gate.

**Deadline pressure.** When the meeting date and the plan collide, the call on
what to cut is the human's — but come with a recommendation: cut a beat, fake
a capability, or ship a shorter demo. Never respond to deadline pressure by
silently lowering the bar; a small flawless demo beats a big rough one.

## What to give the human at each pause

- **Plan/rubric approval:** the narrative, the plan, the rubrics, and 2–3
  lines on the biggest pre-mortem risks and how the plan defuses them.
- **Phase gate:** the verdict, the evidence (screenshots especially), the
  scores, and anything deliberately deferred to a later phase.
- **Escalation:** the score trajectory across rounds, remaining defects, your
  diagnosis of *why* it's stuck, and a recommendation — cut the beat, fake it,
  change approach, or accept with the tradeoff named.
- **Rehearsal:** the whole-demo verdict against the spec goal, the timed
  end-to-end evidence, anything shipped short of the bar — and, if the human
  presents live, a **demo-day runbook**: start command, data-reset command,
  the click path, and known don't-click-that landmines.

The goal of every pause is a *decision*, not a status update. End with a
specific question or a clear approve / change / stop choice.
