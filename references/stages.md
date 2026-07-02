# Stages — detailed playbook

Read this once at the start of a build. It expands each stage from `SKILL.md`
with the questions to ask, what "good" looks like, and how to know you're done
with the stage.

---

## Stage 0 · Intake → `spec.md`

The goal is a spec precise enough that a stranger could judge the result. Capture:

- **Goal** — one sentence. If you can't write it in one sentence, the scope is
  unclear; ask.
- **Users & jobs-to-be-done** — who uses this and what are they trying to
  accomplish. "A busy PM checking deploy status between meetings" beats "users."
- **Non-goals** — what this explicitly does *not* do. Non-goals prevent scope
  creep more effectively than goals do.
- **Constraints** — stack, platform(s), deadline, budget, existing systems to
  integrate with, data sources.
- **References** — screenshots, links, competitor products, an existing app to
  emulate or beat.
- **Taste north-star** — the single most important and most-skipped item. Name
  the products or designers whose bar you're holding this to (e.g. "Linear's
  density and keyboard-first feel", "Stripe's docs clarity"). Without a named
  bar, "taste" scoring is unanchored hand-waving.
- **Design spec** — look for a `DESIGN.md`, design-system doc, design tokens, or
  an existing component library in the project. If one exists, record its path.
  Unlike the taste north-star (an aspirational *bar*), a design spec is a concrete
  *contract* the UI must match — it becomes a must-pass conformance gate in stage 3.
- **`phase_gate_signoff`** setting (on/off) — record it here.

**Done when:** every field is filled or explicitly marked N/A, and nothing is
ambiguous enough to change the plan. If something is, ask now (batched).

---

## Stage 1 · Plan → `plan.md`

Decompose the goal into phases. You choose the granularity per project — there's
no fixed unit — but bias toward **vertical, demoable slices**: each phase should
produce something a person can run and react to. This is what makes
usability/taste judgeable at every gate.

- **UI projects:** slices like "static layout with real data shape" → "core
  interaction working" → "edge states + polish". Each is screenshot-able.
- **CLI / library / backend:** there's no screen to look at, so size phases
  around **independently testable capability** ("parses config + validates" →
  "core transform" → "error handling + DX"). Note in the plan that taste/usability
  here means API ergonomics, error messages, and output legibility — judge those.

For each phase record: **scope** (what's in / out), **deliverable** (the concrete
thing that exists at the end), **dependencies** (what must precede it).

**Spike / de-risk phases.** Not every phase produces a feature. When the plan
rests on an unverified assumption (an unconfirmed third-party API, a feasibility
unknown the pre-mortem raised), add an explicit **spike phase** *before* the phase
that depends on it. A spike is exempt from the full rubric — it gets a
**lightweight go/no-go gate**: "does this work well enough to build on? GO /
PIVOT-to-fallback / STOP." Its deliverable is a findings note + that decision.
Spikes keep load-bearing unknowns from collapsing a later phase.

**Done when:** the phases are ordered, each independently demoable/testable, and
together they cover the spec with no phase large enough to hide a failure.

---

## Stage 2 · Pre-mortem → `premortem.md`

Switch to the **critic** role. The framing that unlocks honest risk-finding:
*"It's six months later. This shipped and it failed/embarrassed us. Walk back the
causes."* Prospective hindsight surfaces risks that "what might go wrong?" misses.

Attack across these axes — at least scan every one:

- **Technical** — the thing that's harder than it looks; the dependency that
  won't behave; the perf cliff.
- **UX & taste** — the flow that's confusing; the screen that'll look generic;
  the interaction that feels janky.
- **Scope** — the phase that's secretly three phases; the "small" feature that
  isn't.
- **Data** — missing, malformed, empty, or huge inputs; auth/rate limits.
- **Integration** — the API that changes shape; the env that differs from local.
- **Edge cases** — empty states, errors, loading, offline, the unhappy paths.
- **Security & secrets** — API keys/creds storage, authz, injection, PII, the
  blast radius if a token leaks.
- **Cost & budget** — runaway LLM/API spend, per-run cost, what an infinite loop
  or a bad input costs you.
- **Ops & reliability** — what has to be running for this to work (gateways,
  schedulers, services), what happens when it isn't, how you'd know it broke.

For each material risk, write a one-line **mitigation** and fold it into
`plan.md` (a guard phase, a spike, a changed sequence, an added rubric item).

**Convergence, not counting:** after revising, run another pass. Stop when a pass
yields **no new material risk** — that's the signal the plan is hardened. Cap at
~3 passes regardless, so you don't stall before code exists. Note in
`premortem.md` why you stopped (converged vs. cap).

**Done when:** the plan reflects the mitigations and a fresh pass finds nothing
new worth changing.

---

## Stage 3 · Rubrics → `rubric.md`

The keystone. Write one acceptance rubric **per phase, before any code**. Format
and pass-condition live in `rubric-format.md` — follow it exactly so the gate
logic is unambiguous. The thinking that goes into each rubric:

- **Must-pass gates** — the binary non-negotiables for this phase. Start from
  the standard production set (tests green, no type/lint errors, no secrets,
  error/empty/loading states handled — see `rubric-format.md`), then add
  phase-specific gates: "Submitting the form persists the record." "No console
  errors on load." If any fails, the phase fails regardless of how pretty it
  is. **If `spec.md` names a design spec and this phase has UI, one of these
  gates is design conformance** — the UI uses the spec's tokens, spacing,
  components, and states, proven by a side-by-side.
- **Quality dimensions** — usability, taste/visual design, robustness,
  performance, accessibility, maintainability — scored numerically with weights
  and **floors** (a floor means "this dimension can't be below X even if the
  weighted total passes", so you can't paper over an ugly UI with fast load
  times, or a rushed codebase with a pretty one).
- **Evidence** — for *every* item, name what proves it. "Screenshot of empty
  state." "Lighthouse a11y ≥ 90." Evidence is what stops the gate from being
  vibes.
- **The panel question** — write the literal prompt the critic will answer:
  *"Would <taste north-star> rate this highly? Where does it fall down?"*
- **Holistic veto** — one dimension that asks "does this feel like something a
  great team shipped?" and can fail a phase that passed every checkbox but feels
  wrong. This is the anti-gaming guard.

Tailor each phase's rubric to that phase — a foundation phase weights robustness
and correctness; a polish phase weights taste and accessibility.

**Done when:** every phase has a rubric, each item has evidence, and you'd be
comfortable being judged by these exact criteria.

→ **HUMAN CHECKPOINT** — present `plan.md` + `rubric.md`, get approval before code.

---

## Stage 4 · Build

Implement the current phase to its deliverable. Build to the rubric, not around
it — if you notice the rubric is wrong (too lax, too strict, missing something),
raise it with the human rather than silently building to a different bar.

**When the spec itself changes mid-build** (the human adds or drops a
requirement, a constraint shifts), don't just absorb it into the current phase.
Update `spec.md`, then revisit every **downstream** rubric the change touches —
those were written up front against the old spec — and re-approve the affected
ones at the next gate. A spec change that silently invalidates a later rubric is
how a build drifts from what was agreed.

**Done when:** the deliverable exists and is runnable/testable enough to gather
the evidence the rubric demands.

---

## Stage 5 · Critique gate → `phase-<n>-eval.md`

Switch to the **critic** role, ideally a fresh subagent given only the spec, the
phase rubric, and access to the running artifact — *not* the builder's notes. In
a single session, manufacture that independence: re-derive from `spec.md` +
`rubric.md` what the phase *should* be before you look at what was built, then
score the build against that derivation rather than rationalizing it.

1. **Gather evidence first.** Run the app. Screenshot the relevant screens and
   states. Capture console/network logs. Run the tests. For CLI/library, capture
   real invocations and their output. Evidence before opinions.
2. **Prefer live, allow fallback.** Score taste/usability from what you observed.
   If you genuinely can't run it, you may score from code — but mark those scores
   **low-confidence**, and treat a low-confidence taste/usability score as a
   trigger to ask the human rather than to assert a pass.
3. **Score every rubric item** against its evidence. Answer the panel question in
   prose — specifically where it falls down, not just a number.
4. **Regression check.** Re-run every prior phase's **must-pass gates** against
   the current build. A phase that breaks an earlier gate fails — record which
   gate regressed in the defect checklist. Gates only; don't re-score earlier
   quality dimensions.
5. **Emit a defect checklist** — specific and actionable ("empty state shows raw
   `null`; needs a friendly message + icon"), not vague ("polish the UI").
6. **Verdict** — apply the rubric's pass condition (see `rubric-format.md`),
   including the borderline band: a weighted score within ~0.2 of the threshold is
   a tie, decided on the holistic veto / panel answer or handed to the human — not
   an automatic pass.

Append each round to `phase-<n>-eval.md` so the score trajectory is visible.

**Done when:** there's a verdict and, if failing, a concrete defect checklist.

---

## Stage 6 · Remediate

If the phase failed, the builder fixes the defect checklist, then the critic
**re-scores** (new round appended to the eval file).

Bounds that keep this from running away:
- **≤ 3 rounds** total.
- **Early-exit:** if a round's score doesn't improve on the prior round, stop —
  you've hit diminishing returns; further mechanical iteration won't help.
- **Escalate** if still failing at 3 rounds (or on early-exit while still
  failing): hand the human the eval, the remaining defects, your diagnosis of
  *why* it's stuck, and a recommendation (cut scope? change approach? accept as-is?).

→ **HUMAN CHECKPOINT** — on pass, get phase-gate sign-off (if
`phase_gate_signoff` is on) before the next phase. On escalation, the human
decides.

**Done when:** the phase passes and is signed off, or the human has ruled on an
escalation.

---

## Stage 7 · Acceptance → `acceptance.md`

Phase gates judge slices. This judges the **product**. Green on every phase can
still add up to something that doesn't cohere — slices that each look fine but
don't flow into each other, a tone that drifts across screens, a goal that's
technically covered but not actually *delivered*. Stage 7 catches that.

Switch to the **critic** role one last time and judge against `spec.md`, not any
phase rubric:

1. **Run the whole thing end to end** — the real user journey from the spec's
   jobs-to-be-done, not isolated features. Gather evidence across the full flow.
2. **Answer the panel question for the product as a whole** — would the taste
   north-star ship *this*, not "is each screen individually okay".
3. **Apply the holistic veto with teeth** — coherence, flow, and whole-product
   taste are exactly what it's for here.
4. **Check the goal is delivered** — re-read the one-sentence goal and the
   non-goals; confirm the built thing is the thing that was asked for, and hasn't
   quietly grown a non-goal.
5. **Check operability** — someone who isn't the builder can run this: setup/run
   docs exist and were followed from scratch, required env/config is documented
   with sane defaults, failures surface intelligibly (logs, error messages)
   rather than silently. Production-grade means inheritable.

A fail here is an **escalation** with a recommendation (what's missing, what it
would take), never a silent ship. For `lite` mode this can be a short paragraph
rather than a full scored pass — but it is never skipped.

→ **HUMAN CHECKPOINT** — present the whole-product verdict and evidence for final
sign-off before calling it done.

**Done when:** the assembled product is signed off against the spec, or the human
has ruled on an acceptance escalation.

---

## Stage 8 · Retro → `retro.md`

After the final phase, reflect on the *process*, not the product:
- Which rubric items never discriminated (always passed/failed)? Cut or sharpen them.
- Where did pre-mortem miss a risk that bit you later? Add that axis next time.
- Where did the harness slow you down for no gain? Trim it.

This is how the methodology compounds across projects.
