# Stages — detailed playbook

Read this once at the start of a build. It expands each stage from `SKILL.md`
with the questions to ask, what "good" looks like, and how you know you're done.

---

## Stage 0 · Intake → `spec.md`

The goal is a spec precise enough that a stranger could judge the demo. Capture:

- **Goal** — one sentence: what this demo must make the audience believe or
  decide. Not "a dashboard for X" but "convince the CFO that real-time spend
  visibility is worth funding".
- **Audience** — who is in the room, by role. What they care about, what they're
  skeptical of, what decision this demo is meant to move. "The COO who thinks
  this is a spreadsheet problem" beats "executives".
- **Demo narrative** — the script. Beats in order: what the presenter says and
  clicks, what appears, and where the **money moment** is. If the human doesn't
  have one, *draft it and get it approved* — this is the spine everything else
  hangs on. Note who drives: presenter click-through, self-driven exec, or
  recorded.
- **Demo context** — projected / screen-shared / laptop-passed-around; target
  resolution and aspect; network available or not (offline-capable is often the
  right call); time slot length.
- **Data story** — what sample data the demo needs to be credible: entities,
  magnitudes, time ranges, and the narrative beats hidden in it (the anomaly to
  find, the trend to reveal). See the demo data doctrine in `SKILL.md`.
- **Deadline** — when is the meeting. This bounds the plan and picks the mode.
- **Non-goals** — what this demo does *not* do. Default candidates: real auth,
  real integrations, persistence, scale, mobile. Non-goals prevent production
  creep more effectively than goals do.
- **Constraints** — stack, platform, existing systems to *appear* integrated
  with, brand guidelines.
- **References** — screenshots, competitor demos, products to emulate or beat.
- **Taste north-star** — name the products or designers whose visual bar this
  is held to (e.g. "Linear's density", "Stripe's restraint", "Palantir's
  command-center gravitas"). Without a named bar, "wow" scoring is unanchored.
- **Design spec** — look for a `DESIGN.md`, brand/design-system doc, tokens, or
  component library. If one exists, record its path: UI must conform, and
  conformance becomes a must-pass gate (stage 3).
- **`mode`** and **`phase_gate_signoff`** — record both.

**Done when:** every field is filled or explicitly N/A, the narrative is
approved, and nothing ambiguous remains that would change the plan. If
something is, ask now (batched).

---

## Stage 1 · Plan → `plan.md`

Phase along the demo path — the narrative is the decomposition:

1. **Money moment first** (or as early as it can stand): the beat the meeting
   exists for gets the most remediation runway.
2. **Narrative spine:** the remaining beats of the script, in the order that
   lets each phase end demoable.
3. **Polish pass:** transitions, loading states as theater (skeletons, not
   spinners), typography and spacing sweep against the north-star.
4. **Off-script resilience pass:** everything visible-but-unscripted either
   works shallowly or is gracefully inert; no dead ends, no default framework
   styling anywhere reachable.

Every phase is demoable — wow is only judgeable on something a person can see
and click. For each phase record: **scope** (in / out / **stubbed** — stubbing
is an explicit design decision, not an omission), **deliverable**,
**dependencies**.

**Spike phases.** When the plan rests on an unverified assumption (an unproven
API, "can we render 10k points smoothly?"), add a **spike** before the phase
that depends on it. Spikes skip the full rubric and get a **go/no-go gate**:
GO / PIVOT-to-fallback / STOP, plus a findings note. For a demo, the fallback
is often "fake it convincingly" — that's a legitimate PIVOT; record it so
nobody later mistakes the stub for a real integration.

**Deadline check.** Sum the phases against the meeting date, including
remediation slack (assume at least one phase needs two rounds). If it doesn't
fit, cut scope *by beat* — drop or fake a whole narrative beat — rather than
thinning quality across all of them. A short flawless demo beats a long rough
one.

**Done when:** phases cover the narrative, each ends demoable, the money moment
is early, an off-script pass exists, and the plan fits the deadline.

---

## Stage 2 · Pre-mortem → `premortem.md`

Switch to the **critic**. Framing: *"The demo just happened and it flopped —
walk back the causes."* Prospective hindsight beats "what might go wrong?".

Attack across these axes — scan every one:

- **On-stage failure** — crash, blank screen, unhandled state on the scripted
  path; cold-start problems; the resize/projector surprise.
- **Generic look** — "seen this template before"; default component-library
  face; charts that look like chart-library defaults.
- **Data credibility** — numbers an insider would laugh at; inconsistent
  totals; timestamps from last year; obviously fake names.
- **Narrative** — the money moment that needs too much explanation; a beat
  that's a feature tour, not a story; a demo longer than the slot.
- **Off-script** — the exec grabs the mouse; the click into a dead end; the
  question ("what happens if I…") the demo can't survive.
- **Environment** — wifi dies; screen share latency makes animation janky;
  wrong resolution clips the layout; OS notifications pop over the demo.
- **Scope & deadline** — the phase that's secretly three; polish time consumed
  by production creep (auth, tests, real integrations nobody sees).
- **Technical** — the thing harder than it looks; the perf cliff at
  presentation data volume; the flaky dependency.
- **Sensitive data** — real customer names/numbers accidentally in the seed
  data; anything on screen that can't be shown to this audience.

For each material risk, write a one-line **mitigation** and fold it into
`plan.md` (a spike, a changed sequence, an added rubric gate, an offline
fallback, a scope cut).

**Convergence, not counting:** after revising, run another pass. Stop when a
pass yields **no new material risk**; cap at ~3 passes. Note the stop reason in
`premortem.md`.

**Done when:** the plan reflects the mitigations and a fresh pass finds nothing
new worth changing.

---

## Stage 3 · Rubrics → `rubric.md`

The keystone. One rubric **per phase, before any code**. Format and pass
condition live in `rubric-format.md` — follow it exactly. The thinking:

- **Must-pass gates** — binary non-negotiables. Standard set for demo phases:
  the phase's narrative beats run clean end to end; **zero placeholder content
  visible in any reachable state**; no console errors on the path; correct at
  the presentation resolution; design-spec conformance if `spec.md` names one
  (side-by-side evidence — conformance is binary, not a taste score). Add
  phase-specific gates ("the anomaly appears in the chart at beat 3").
- **Quality dimensions** — wow / first impression, narrative flow, data
  credibility, polish & taste, perceived performance — weighted, with
  **floors** so a stunning-but-janky phase can't buy its way through. Tailor
  weights: the money-moment phase weights wow heavily; the resilience pass
  weights robustness-on-path.
- **Evidence** — every item names what proves it ("screenshot of beat 2 at
  1920×1080", "timed cold start < 3s"). No evidence, no measurable item —
  rewrite until it has one.
- **The panel question** — the literal prompt the critic answers: *"Would
  <this audience>, holding it to <taste north-star>, lean forward — and where
  does it fall flat?"*
- **Holistic veto** — the anti-gaming backstop: every box ticked but it looks
  like a template / the beat doesn't land → FAIL.

**Done when:** every phase has a rubric, each item has evidence, and you'd be
comfortable being judged by these exact criteria.

→ **HUMAN CHECKPOINT** — present narrative + `plan.md` + `rubric.md`; get
approval before code.

---

## Stage 4 · Build

Implement the current phase to its deliverable. Build to the rubric, not
around it — if the rubric turns out wrong (too lax, too strict, missing
something), raise it with the human rather than silently building to a
different bar.

- **Stubbing doctrine:** on-path is real; visible-but-off-path is handsomely
  stubbed (real styling, plausible content, gracefully inert) or hidden.
  Nothing reachable shows default framework styling, empty grey boxes, or
  broken affordances.
- **Data:** seed per the spec's data story, deterministic, checked for
  internal consistency. Never generate placeholder or random content.
- **Spec changes mid-build:** if the human adds/drops a requirement or the
  narrative shifts, update `spec.md`, then revisit every **downstream** rubric
  the change touches and re-approve at the next gate. A silent spec change
  invalidating later rubrics is how a demo drifts from what was agreed.

**Done when:** the deliverable runs well enough to gather the evidence the
rubric demands.

---

## Stage 5 · Critique gate → `phase-<n>-eval.md`

Switch to the **critic** — ideally a fresh subagent given only the spec, the
phase rubric, and the running artifact, *not* the builder's notes. In a single
session, manufacture independence: re-derive from `spec.md` + `rubric.md` what
this phase of the demo *should* look and feel like before looking at what was
built.

1. **Gather evidence first.** Run the app. Screenshot every scripted state and
   every reachable off-path state. Time the interactions. Capture console
   logs. Evidence before opinions.
2. **Prefer live, allow fallback.** Wow, credibility, and flow cannot be
   judged from source. If you genuinely can't run it, score from code but mark
   those scores **low-confidence** — and treat a low-confidence score on wow or
   credibility as a trigger to ask the human, not to assert a pass.
3. **Score every rubric item** against its evidence. Answer the panel question
   in prose — where it falls flat, specifically.
4. **Demo-path regression.** Re-walk every prior phase's narrative beats and
   re-run their must-pass gates against the current build. A phase that breaks
   an earlier beat fails, whatever its own scores. Gates and beats only —
   don't re-score earlier quality dimensions.
5. **Emit a defect checklist** — specific and actionable ("beat 3 chart
   animates in at 400ms but the tooltip shows `undefined` for week 12"), not
   vague ("polish the charts").
6. **Verdict** per the rubric's pass condition, including the borderline band:
   within ~0.2 of threshold is a tie — resolve on the veto and panel answer or
   hand to the human.

Append each round to `phase-<n>-eval.md` so the score trajectory is visible.

**Done when:** there's a verdict and, if failing, a concrete defect checklist.

---

## Stage 6 · Remediate

Builder fixes the defect checklist; critic **re-scores** (new round appended).

Bounds:
- **≤ 3 rounds** total.
- **Early-exit** if a round doesn't improve the score — diminishing returns.
- **Escalate** if still failing at 3 rounds or on early-exit: hand the human
  the eval history, remaining defects, your diagnosis of *why* it's stuck, and
  a recommendation — cut the beat, fake the capability, change approach, or
  accept with the tradeoff named. Deadline pressure makes "cut the beat" a
  respectable answer; a silent quality drop is not.

→ **HUMAN CHECKPOINT** — on pass, phase-gate sign-off (if `phase_gate_signoff`
is on). On escalation, the human decides.

**Done when:** the phase passes and is signed off, or the human has ruled.

---

## Stage 7 · Dress rehearsal → `rehearsal.md`

Phase gates judge beats. This judges the **performance**. Green on every phase
can still flop in the room — beats that don't flow, tone that drifts between
screens, a demo that runs long. The rehearsal catches that.

As the **critic**, under presentation conditions:

1. **Cold start.** Fresh launch, seeded data, target resolution, network
   conditions per spec (including offline if that's the plan). No warm caches,
   no dev server niceties the meeting room won't have.
2. **Perform the full narrative**, timed against the slot. Every beat, in
   order, at presenter pace. Gather evidence across the whole run.
3. **Go off-script.** Click what a curious exec would click. Resize. Click the
   thing twice. Nothing may visibly dead-end or drop to default styling.
4. **Answer the panel question for the whole demo** — would this audience lean
   forward at *this performance*, not "is each screen individually fine".
5. **Check the goal.** Re-read the one-sentence goal and non-goals: does the
   performed demo actually move the decision it was built to move? Has it
   quietly grown a non-goal?
6. **Holistic veto with teeth** — coherence, flow, and whole-demo credibility
   are exactly what it's for here.

A fail is an **escalation** with a recommendation (what's flat, what it would
take), never a silent ship. In `lite` mode this can be a short note — but the
end-to-end cold-start run-through is never skipped.

→ **HUMAN CHECKPOINT** — present the verdict, the timing, and the evidence for
final sign-off. If the human will present live, hand over a **demo-day
runbook**: start command, reset-data command, the click path, and known
don't-click-that landmines.

**Done when:** the performed demo is signed off against the spec, or the human
has ruled on an escalation.

---

## Stage 8 · Retro → `retro.md`

After sign-off, reflect on the *process*, not the product:

- Which rubric items never discriminated (always passed/failed)? Cut or
  sharpen them.
- What did the pre-mortem miss that bit you later? Add that axis next time.
- Where did the harness slow you down for no gain? Trim it.
- What did the rehearsal catch that phase gates should have caught earlier?

This is how the methodology compounds across demos.
