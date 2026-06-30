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

- **Must-pass gates** — the binary non-negotiables for this phase. "Submitting
  the form persists the record." "No console errors on load." If any fails, the
  phase fails regardless of how pretty it is.
- **Quality dimensions** — usability, taste/visual design, robustness,
  performance, accessibility — scored numerically with weights and **floors** (a
  floor means "this dimension can't be below X even if the weighted total
  passes", so you can't paper over an ugly UI with fast load times).
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

**Done when:** the deliverable exists and is runnable/testable enough to gather
the evidence the rubric demands.

---

## Stage 5 · Critique gate → `phase-<n>-eval.md`

Switch to the **critic** role, ideally a fresh subagent given only the spec, the
phase rubric, and access to the running artifact — *not* the builder's notes.

1. **Gather evidence first.** Run the app. Screenshot the relevant screens and
   states. Capture console/network logs. Run the tests. For CLI/library, capture
   real invocations and their output. Evidence before opinions.
2. **Prefer live, allow fallback.** Score taste/usability from what you observed.
   If you genuinely can't run it, you may score from code — but mark those scores
   **low-confidence**, and treat a low-confidence taste/usability score as a
   trigger to ask the human rather than to assert a pass.
3. **Score every rubric item** against its evidence. Answer the panel question in
   prose — specifically where it falls down, not just a number.
4. **Emit a defect checklist** — specific and actionable ("empty state shows raw
   `null`; needs a friendly message + icon"), not vague ("polish the UI").
5. **Verdict** — apply the rubric's pass condition (see `rubric-format.md`).

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

## Stage 8 · Retro → `retro.md`

After the final phase, reflect on the *process*, not the product:
- Which rubric items never discriminated (always passed/failed)? Cut or sharpen them.
- Where did pre-mortem miss a risk that bit you later? Add that axis next time.
- Where did the harness slow you down for no gain? Trim it.

This is how the methodology compounds across projects.
