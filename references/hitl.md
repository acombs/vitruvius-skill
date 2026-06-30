# Human-in-the-loop policy

The human is a control surface, not a spectator. Two distinct kinds of pause;
support both, and don't collapse them into one.

## 1. Clarification pauses (anytime, including mid-stage)

Trigger the moment the goal or a requirement is **genuinely ambiguous** — where
two reasonable readings would lead to different builds. A wrong assumption made
silently in stage 0 compounds through every phase, so the cheapest moment to
resolve it is now.

Rules:
- **Batch.** Collect the open questions and ask them together. Five separate
  interruptions is a worse experience than one block of five questions. The
  exception is a true blocker discovered mid-build that nothing else can proceed
  without.
- **Don't fabricate to avoid asking.** If you don't know whether the dashboard is
  for desktop or mobile, ask — don't pick one and bury the assumption.
- **Make it answerable.** Offer concrete options with a recommendation when you
  can ("desktop-first, or responsive from day one? I'd suggest desktop-first
  because the references are all wide layouts"), not open-ended "what do you
  want?".
- **Distinguish ambiguity from preference.** Conventional defaults you can verify
  (which test runner, tabs vs spaces matching the repo) don't need a question —
  decide, state it, move on. Reserve questions for decisions that are genuinely
  the human's to make.

## 2. Gate-approval pauses (at boundaries)

Four mandatory checkpoints:

- **(a) After plan + pre-mortem, before any code.** Present `plan.md` and
  `rubric.md`. This is the highest-leverage checkpoint — changing the plan here
  costs a sentence; changing it after three phases costs days. Always on.
- **(b) At every phase gate.** When a phase passes its rubric, get sign-off
  before the next phase begins. Governed by the **`phase_gate_signoff`** flag
  (default **on**). When off, passing phases advance automatically and you only
  pause for (a), (c), (d), and clarifications.
- **(c) On escalation.** When a phase can't pass its rubric within 3 remediation
  rounds (or hits early-exit while still failing), stop and hand it to the human.
  Always on — never silently advance past a failing phase, never loop forever.
- **(d) Final acceptance (stage 7).** When all phases are done, present the
  whole-product verdict against `spec.md` for sign-off before calling it shipped.
  Always on — green phases are not the same as a delivered goal.

**Mid-build spec change.** If the human changes a requirement mid-build, that's
not a normal clarification — it can invalidate rubrics already written for later
phases. Update `spec.md`, flag which downstream rubrics the change touches, and
re-approve those at the next gate rather than absorbing the change silently.

## What to give the human at each pause

- **Plan/rubric approval:** the plan, the rubrics, and a 2–3 line summary of the
  biggest risks the pre-mortem surfaced and how the plan now handles them.
- **Phase gate:** the verdict, the evidence (screenshots especially), the final
  scores, and anything you deliberately deferred to a later phase.
- **Escalation:** the eval history (score trajectory across rounds), the
  remaining defects, your diagnosis of *why* it's stuck, and a recommendation —
  cut scope, change approach, or accept as-is with the tradeoff named.
- **Final acceptance:** the whole-product verdict against the spec goal, the
  end-to-end evidence, and anything deferred or knowingly shipped short of the bar.

The goal of every pause is a *decision*, not a status update. Always end with a
specific question or a clear "approve / change / stop" choice.
