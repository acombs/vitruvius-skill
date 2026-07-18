---
name: vitruvius
description: >-
  A quality-gated methodology for building production-grade applications (or
  any substantial software artifact) from a goal + requirements. Plans the
  work, runs an adversarial pre-mortem to harden the plan, defines acceptance
  rubrics UP FRONT (explicit taste, usability, robustness, and maintainability
  bars), then builds in phases — each passing a separate critic's gate and a
  bounded remediation loop, with human checkpoints throughout. Use whenever
  the user hands over a goal and requirements and wants it built carefully
  rather than fast — production-ready, production-grade, hardened,
  ship-quality, maintainable, "do it right", for real users; or asks for a
  plan or pre-mortem before coding, wants success criteria or rubrics defined,
  or cares about taste/usability/UX quality. Trigger even if they don't say
  "vitruvius" by name, as long as they're describing a deliberate,
  quality-critical build from requirements. Do NOT use for quick one-off
  scripts, small edits, or demo/prototype apps built to impress an audience —
  for those use the vitruvius-demo skill.
---

# Vitruvius

A harness for turning *"here's what I want, here are the requirements"* into a
built artifact that a great team would be proud to ship — by making the plan
survive an adversarial pre-mortem, committing to an explicit quality bar
*before* writing code, and refusing to advance past a phase until it clears that
bar (or you, the human, override).

Named for the Roman architect whose treatise held that good building must have
*firmitas, utilitas, venustas* — be **durable, useful, and beautiful**. That is
this skill's quality bar too: the rubrics judge robustness, usability, and taste,
and a phase advances only when all three hold.

The core conviction: **the bar is set before the work, judged by someone other
than the builder, against the running thing — not the code.** Most quality
failures come from grading your own homework after the fact and quietly lowering
the bar to match what you built.

## When to use this

Use it for deliberate, quality-critical builds from a stated goal + requirements
— especially when taste and usability matter and the result must survive real
users, real data, and future maintainers. Don't use it for throwaway scripts or
tiny edits (the ceremony costs more than it returns), and don't use it for
demo/prototype apps whose job is to impress a room — that's the sibling
**vitruvius-demo** skill, with deliberately opposite trade-offs (happy-path
depth over edge-case breadth).

**Right-size the ceremony — `mode`.** This harness is not free: separate critic
passes, multiple pre-mortem rounds, and up to three remediation rounds per phase
add up on metered usage. Match the weight to the stakes, and record the choice in
`spec.md`:

- **`lite`** — small or low-stakes builds. One pre-mortem pass, **gates-only**
  rubrics (skip the weighted quality scores), critic as an explicit role-switch
  rather than a subagent, `phase_gate_signoff` off. Keeps the spine — bar before
  code, judge against the running thing, regression check — without the full cost.
- **`full`** (default) — taste-critical or production builds. Everything in this
  document as written.

When in doubt, start `lite` and escalate a specific phase to `full` if it proves
to carry the project's taste or risk.

## The lifecycle at a glance

```
0. INTAKE      → capture goal, users, non-goals, references, taste north-star   ┐
1. PLAN        → decompose into phases (the agent sizes the phases)             │ setup
2. PRE-MORTEM  → adversarially attack the plan, revise, converge                │
3. RUBRICS     → define per-phase acceptance bar UP FRONT (hybrid format)       ┘
   ── HUMAN CHECKPOINT: approve plan + rubrics before any code ──
┌─ per phase ───────────────────────────────────────────────────────┐
│ 4. BUILD      → implement the phase                                 │
│ 5. CRITIQUE   → separate critic scores it against the rubric +      │
│                 re-runs prior phases' gates (regression)            │
│ 6. REMEDIATE  → fix the defect list; re-score; ≤3 rounds, early-exit│
│    ── HUMAN CHECKPOINT: sign off the gate (or escalate on failure) ─│
└────────────────────────────────────────────────────────────────────┘
7. ACCEPTANCE  → judge the assembled WHOLE against spec.md (holistic) ┐ finish
   ── HUMAN CHECKPOINT: final sign-off ──                             │
8. RETRO       → what to change about the *methodology* next time     ┘
```

At any point, if the goal or a requirement is genuinely ambiguous, **stop and
ask** (see Human-in-the-loop below).

## State lives on disk

Agent context is fragile across a long build, so the artifacts *are* the memory
and the audit trail. Create a `.vitruvius/` directory in the target project
and write everything there:

```
.vitruvius/
├── spec.md            # stage 0
├── plan.md            # stage 1 (updated by stage 2)
├── premortem.md       # stage 2
├── rubric.md          # stage 3 (one rubric block per phase)
├── phase-<n>-eval.md  # stage 5/6, appended each remediation round
├── acceptance.md      # stage 7 (whole-product gate against spec.md)
└── retro.md           # stage 8
```

Templates for each live in `assets/templates/`. Read the template before writing
the artifact the first time. Keep these files current — if you change the plan,
update `plan.md`; later stages read from disk, not from memory.

## Committing your work

Commit at every **meaningful unit of work** — the test for "meaningful" is simple:
*would it cost real effort to reconstruct if it were lost?* If yes, it's a commit.
That's the whole guide; it rules out both extremes.

- **Not too small:** don't commit every file save or half-written function.
  Nothing of value is lost if an incomplete edit vanishes.
- **Not too large:** don't let a whole multi-phase build pile up into one commit.
  A commit that mixes three phases is impossible to revert cleanly or review.

The stage boundaries are natural commit points and a good default rhythm: the
approved `plan.md` + `rubric.md`, each phase that passes its gate, the
remediation that fixed a failing gate, and the final acceptance. Write a message
that says *what changed and why* (the phase, the gate it cleared), not "wip".
**Push to origin at the same checkpoints** — a passed phase is durable work worth
backing up; don't leave a week of green phases living only on the local machine.

## Roles: builder vs critic

A builder grading its own work grades leniently and rationalizes. Keep the roles
separate:

- **Builder** — does stages 0, 1, 4, 6. The implementer.
- **Critic** — does stages 2 (pre-mortem) and 5 (critique gate). Adversarial
  brief: its job is to find what's wrong, not to be encouraging.

If subagents are available, run the critic as a *separate subagent* with only the
spec, rubric, and evidence — not the builder's reasoning — so it can't inherit
the builder's blind spots. If subagents aren't available, switch roles
explicitly ("Now acting as the critic, with an adversarial brief…") and judge
against the artifacts and evidence, never against your own intentions.

In a single session the role-switched critic shares the builder's context, so its
independence is something you have to manufacture: **before looking at what was
built, re-derive from `spec.md` + `rubric.md` alone what the phase *should* do and
look like**, then score the artifact against that derivation. Judging from the
spec forward rather than the build backward is what keeps the critic from
rubber-stamping the builder's choices.

## The stages

The full playbook for each stage is in `references/stages.md` — **read it before
running the stages.** The essentials:

**0 · Intake → `spec.md`.** Capture: the goal in one sentence; target users and
their jobs-to-be-done; explicit non-goals; constraints (stack, deadlines,
platforms); references/screenshots; and a **taste north-star** — name the
products or designers whose bar this is held to. For a web UI, express the
north-star as a **venustas style profile** (a file in the `venustas` skill's
`profiles/` directory, or a custom one from its template) — that makes the
taste bar enforceable at stage 5 instead of vibes. **Check for a design spec** — a
`DESIGN.md`, design-system doc, Figma/tokens file, or component library in the
project. If one exists, record its location; UI work must conform to it, and that
conformance becomes a must-pass gate (stage 3). If any of this is missing or
ambiguous, ask now (batched).

**1 · Plan → `plan.md`.** Decompose into phases. *You* size the phases based on
the project — but bias toward **vertical, demoable slices**, because you can only
judge usability/taste on something a person can run and see. For
CLI/library/backend work where there's no UI to look at, size phases around
independently testable capability instead, and say so in the plan. Each phase:
scope, deliverable, dependencies.

A phase doesn't have to be demoable if its job is to *de-risk*. When the plan
depends on an unverified assumption — a third-party API you haven't confirmed, a
feasibility unknown surfaced by the pre-mortem — add an explicit **spike phase**.
A spike isn't graded by the full rubric; it has a **lightweight go/no-go gate**:
"does this work well enough to build on, yes/no, and if no, what's the fallback?"
Its deliverable is a findings note + a decision (GO / PIVOT-to-fallback / STOP),
not a feature. Put spikes before the phase that depends on them.

**2 · Pre-mortem → `premortem.md`.** Acting as the critic: *"It's launched and
it failed. Why?"* Attack across technical / UX & taste / scope / data /
integration / edge-cases / **security & secrets / cost & budget / ops &
reliability**. Write the risks down, then **revise `plan.md`** to
defuse the material ones. Loop — but **converge, don't just count**: stop when a
round surfaces no new *material* risk. Cap at a few rounds so it doesn't become
analysis paralysis before any code exists.

**3 · Rubrics → `rubric.md`.** This is the keystone. *Before any code*, write one
acceptance rubric per phase. Use the **hybrid format** (see
`references/rubric-format.md`): a set of binary **must-pass gates** for
correctness/non-negotiables — the standard production set: tests pass green, no
type/lint errors, no secrets in code or logs, error/empty/loading states
handled on every screen or path this phase touches — plus weighted **numeric
quality scores** (with per-dimension floors) for the softer dimensions:
usability, taste/visual design, robustness, performance, accessibility, and
**maintainability** (a future maintainer inherits this codebase — structure,
naming, tests as documentation). Each item names the **evidence**
that proves it. Include the panel framing explicitly: *"Would <the taste
north-star experts> rate this highly? Where does it fall down?"* and keep one
holistic dimension that can veto a high checklist score when something
technically-passes-but-feels-wrong. **If a design spec exists (stage 0), every
phase with UI gets a must-pass gate:** *"UI elements conform to `DESIGN.md`
(tokens, spacing, components, states) — Evidence: side-by-side with the spec."*
Conformance is binary, not a taste score — drifting from a provided design system
fails the phase regardless of how good it looks on its own.

→ **HUMAN CHECKPOINT.** Present `plan.md` + `rubric.md` and get approval before
writing any code. This is the highest-leverage moment to course-correct.

**4 · Build.** Implement the current phase to its deliverable. Build to the
rubric, not around it.

**5 · Critique gate → `phase-<n>-eval.md`.** Acting as the critic: run the thing,
gather **evidence**, and score every rubric item. Prefer live evidence —
screenshots of the running app, console logs, actual output — especially for
taste/usability, which cannot be judged from source. If no preview/run is
possible, you may evaluate from code, but **stamp those scores low-confidence**,
and treat a low-confidence taste score as a reason to ask the human. Also
**re-run every prior phase's must-pass gates as a regression check** — a new
phase that breaks an earlier gate fails, however good its own rubric looks (gates
only; you don't re-score earlier quality dimensions). **If a design spec exists,
check UI against it directly** — pull up `DESIGN.md` and compare tokens, spacing,
and component usage, don't eyeball it. **If the phase has a web UI, run the
`venustas` skill as the taste portion of this gate**: its deterministic audit
(contrast, overflow, console errors) becomes must-pass gates, and its
code-blind judge's scores feed the rubric's taste/visual-design rows against
the profile chosen at intake. In `lite` mode run its judge as a role-switch
rather than a subagent. If venustas isn't installed, fall back to this
stage's own screenshot-based taste evaluation as written. Emit a **defect checklist**
(specific, actionable) and a pass/fail verdict per the rubric's pass condition.
**Borderline rule:** a weighted score within ~0.2 of the threshold is a tie, not
a pass — resolve it on the holistic veto and the panel answer, or hand it to the
human; don't let 3.51 mechanically advance.

**6 · Remediate.** If it didn't pass, fix the defect checklist, then **re-score**
(append a new round to `phase-<n>-eval.md`, tracking the score delta). Bounds:
- **≤ 3 rounds.**
- **Early-exit** if a round doesn't improve the score (diminishing returns).
- If still failing after 3 rounds → **escalate to the human** rather than
  looping forever or quietly advancing.

→ **HUMAN CHECKPOINT.** When a phase passes, get the human's sign-off before
starting the next phase (this is on by default; see the config flag below). On
escalation, hand the human the eval, the defect list, and your recommendation.

**7 · Acceptance → `acceptance.md`.** Passing every phase is not the same as
delivering the goal. Once the last phase clears, judge the **assembled whole**
against `spec.md`, not against any single phase rubric: does it deliver the
one-sentence goal, for the named users' jobs-to-be-done, to the taste north-star?
This is where coherence across slices, end-to-end flow, and whole-product taste
get caught — none of which a phase gate can see. Run the full thing, gather
evidence, answer the panel question for the product as a whole, and apply the
**holistic veto** with teeth. Also check **operability**: someone who isn't you
can run this — setup/run documentation exists, required env/config is
documented with sane defaults, and failure modes surface intelligibly (logs,
error messages) rather than silently. A fail here is an escalation, not a
silent ship.

**8 · Retro → `retro.md`.** After the last phase, reflect on the *methodology*:
what slowed you down, which rubric items didn't discriminate, what to change next
time. This is how the harness improves.

## Human-in-the-loop

Two distinct kinds of pause — support both. Full policy in
`references/hitl.md`; the defaults:

1. **Clarification (anytime).** The moment the goal or a requirement is genuinely
   ambiguous, stop and ask — including mid-stage, not only at gates. **Batch**
   the questions (ask all five at once, not five interruptions). Don't invent a
   requirement to avoid asking; a wrong assumption compounds across phases.
2. **Gate approval (at boundaries).** Pause for sign-off (a) after plan +
   pre-mortem before any code, (b) at every phase gate, and (c) on escalation
   when a phase can't pass in 3 rounds.

**Config flag — `phase_gate_signoff`.** Per-gate human sign-off (1b) defaults to
**on**, which makes this a check-in-often co-pilot — the right default for
taste-critical work. For low-stakes builds the human can set it to **off**, and
then phases that pass their rubric advance automatically; you still pause for
plan approval, real ambiguity, and escalation. Record the chosen setting near the
top of `spec.md` so it survives context loss.

## Two failure modes to design against

- **Rubric-gaming** — optimizing the letter of the rubric, not the spirit. The
  holistic veto dimension (stage 3) is the guard; if it passes every checkbox but
  a great designer would wince, it fails.
- **Over-planning paralysis** — pre-mortem rounds with diminishing returns,
  burning budget before code exists. The convergence rule (stage 2) is the guard.
- **Phase-local blindness** — every slice passes, but the assembled product
  doesn't cohere. The regression check (stage 5) and the whole-product acceptance
  gate (stage 7) are the guards.

## Anti-patterns

- Generating the rubric *after* building the phase. The bar must precede the work.
- Letting the builder also be the judge with no role separation.
- Scoring taste/usability from source code and reporting it as confident.
- Treating "max 3 rounds" as "always do 3 rounds" — early-exit on no improvement.
- Silently advancing past a failing phase, or looping a phase forever.
- Shipping on green phases without ever judging the assembled whole (stage 7).
- Letting a phase break an earlier phase's gate — re-run prior gates every time.
- Reading a near-threshold weighted score as a pass — the borderline band is a tie.
- Treating maintainability as optional polish — the next developer is a user too.
- Calling it done with no run docs and undocumented env/config — operability is
  part of acceptance (stage 7).
