---
name: vitruvius-demo
description: >-
  A quality-gated harness for building highly-functional, useful, and beautiful
  demo/prototype applications for C-level and executive audiences. Plans the
  build around the demo narrative, adversarially pre-mortems demo-day failure
  modes, defines the acceptance bar UP FRONT (first-impression wow, data
  credibility, zero-crash demo path), then builds in phases — each passing a
  separate critic's gate — and ends with a full dress rehearsal. Use whenever
  the user wants a demo, prototype, POC, proof of concept, pilot, showcase, or
  "make this mockup real" app — especially for C-suite, board, customer,
  investor, partner, or sales audiences — or says things like "make it look
  impressive", "for the exec review", "something I can present Thursday",
  "pitch demo", "innovation showcase". Trigger even if they don't say
  "vitruvius" or "demo" by name, as long as the deliverable is an application
  whose job is to impress and persuade an audience rather than run in
  production. Do NOT use for production systems (use the vitruvius skill for
  those), quick one-off scripts, or small edits.
---

# Vitruvius Demo

A harness for turning *"here's what I want to show the execs"* into a demo
application that wins the room — by planning around the **demo narrative**,
committing to an explicit quality bar *before* writing code, and refusing to
advance past a phase until a separate critic says it clears that bar.

Named for the Roman architect whose treatise held that good building must have
*firmitas, utilitas, venustas* — durable, useful, beautiful. For a demo app
that translates directly: **firmitas** — it never breaks on stage; **utilitas**
— the narrative lands and the audience gets it; **venustas** — it looks like
the future in the first five seconds.

The core conviction: **the bar is set before the work, judged by someone other
than the builder, against the running thing — not the code.** Most demo
failures come from grading your own homework after the fact — and from
polishing features the audience will never see while the money moment stays
rough.

## What a demo app is (and isn't)

A demo is judged in a 20-minute meeting by people who decide budgets. That
changes the engineering calculus:

- **The demo path is sacred.** The exact scripted click-path must be flawless —
  fast, beautiful, crash-proof. Off the path, things may be stubbed, but
  nothing visible may look broken or dead-end.
- **Credibility lives in the data.** Realistic names, plausible magnitudes,
  coherent time series, the audience's own domain vocabulary. One "Lorem
  ipsum" or `user_123` costs more trust than a missing feature.
- **Perceived quality beats actual coverage.** Instant-feeling interactions and
  polished visible states outrank test coverage, security hardening, and edge
  cases no one will trigger in the room.
- **It is not production.** Don't build auth, ops, or scale nobody will see.
  Say so in the spec's non-goals — and tell the human if they're drifting into
  production requirements mid-build (that's the sibling **vitruvius** skill's
  territory).

## When to use this

Use it for demos, prototypes, and POCs whose audience is executives, boards,
customers, or investors — anywhere first impressions and persuasion are the
point. Don't use it for production systems (its trade-offs are wrong for
that), throwaway scripts, or tiny edits.

**Right-size the ceremony — `mode`.** Record the choice in `spec.md`:

- **`lite`** — quick prototype / internal show-and-tell. One pre-mortem pass,
  gates-only rubrics (skip weighted scores), critic as an explicit role-switch,
  `phase_gate_signoff` off. Keeps the spine — bar before code, judge the
  running thing, demo-path regression check.
- **`full`** (default) — flagship demo: board meeting, customer pitch, keynote.
  Everything in this document as written, including the dress rehearsal.

When the deadline is tight, prefer `lite` mode over skipping stages — the
stages are cheap; skipping the rubric or the rehearsal is how demos die live.

## The lifecycle at a glance

```
0. INTAKE      → audience, decision at stake, DEMO NARRATIVE, data story,      ┐
                 taste north-star, deadline                                     │
1. PLAN        → phases along the demo path: money moment first, then outward  │ setup
2. PRE-MORTEM  → attack the plan with demo-day failure modes, revise, converge │
3. RUBRICS     → per-phase acceptance bar UP FRONT (wow, credibility, path)    ┘
   ── HUMAN CHECKPOINT: approve narrative + plan + rubrics before any code ──
┌─ per phase ───────────────────────────────────────────────────────┐
│ 4. BUILD      → implement the phase                                 │
│ 5. CRITIQUE   → separate critic scores it against the rubric +      │
│                 re-walks the demo path so far (regression)          │
│ 6. REMEDIATE  → fix the defect list; re-score; ≤3 rounds, early-exit│
│    ── HUMAN CHECKPOINT: sign off the gate (or escalate on failure) ─│
└────────────────────────────────────────────────────────────────────┘
7. REHEARSAL   → full dress rehearsal of the demo script, cold start,  ┐ finish
                 at presentation conditions — judged against spec.md   │
   ── HUMAN CHECKPOINT: final sign-off ──                              │
8. RETRO       → what to change about the *methodology* next time      ┘
```

At any point, if the goal, audience, or narrative is genuinely ambiguous,
**stop and ask** (see Human-in-the-loop below).

## The demo narrative

The single most important intake artifact. It's the script of the meeting:
what the presenter says and clicks, in what order, and where the **money
moment** is — the beat where the audience leans forward. Everything else
derives from it:

- The **plan** phases along it (money moment earliest it can stand).
- The **rubrics** weight what the audience actually sees.
- The **regression check** is literally re-walking the path.
- The **rehearsal** is performing it end to end.

If the human doesn't have a narrative yet, drafting one *is* stage 0 work —
propose one and get it approved. A demo built without a script is a feature
tour, and feature tours lose rooms.

## State lives on disk

Agent context is fragile across a build, so the artifacts *are* the memory and
the audit trail. Create a `.vitruvius/` directory in the target project:

```
.vitruvius/
├── spec.md            # stage 0 — includes audience, narrative, data story
├── plan.md            # stage 1 (updated by stage 2)
├── premortem.md       # stage 2
├── rubric.md          # stage 3 (one rubric block per phase)
├── phase-<n>-eval.md  # stage 5/6, appended each remediation round
├── rehearsal.md       # stage 7 (dress rehearsal against spec.md)
└── retro.md           # stage 8
```

Templates for each live in `assets/templates/`. Read the template before
writing the artifact the first time. Keep these files current — later stages
read from disk, not from memory.

## Committing your work

Commit at every **meaningful unit of work** — the test: *would it cost real
effort to reconstruct if lost?* Stage boundaries are the natural rhythm: the
approved plan + rubrics, each phase that passes its gate, each remediation
round that changed the verdict, the rehearsal. Messages say *what changed and
why* (the phase, the gate it cleared), not "wip". **Push to origin at the same
checkpoints** — a passed phase is durable work worth backing up.

## Roles: builder vs critic

A builder grading its own work grades leniently. Keep the roles separate:

- **Builder** — stages 0, 1, 4, 6. The implementer.
- **Critic** — stages 2 (pre-mortem), 5 (critique gate), 7 (rehearsal).
  Adversarial brief: find what's wrong; channel the toughest exec in the room.

If subagents are available, run the critic as a *separate subagent* given only
the spec, rubric, and evidence — not the builder's reasoning. If not, switch
roles explicitly and manufacture independence: **before looking at what was
built, re-derive from `spec.md` + `rubric.md` what this phase of the demo
*should* look and feel like**, then judge the artifact against that derivation
— spec-forward, not build-backward.

## The stages

The full playbook is in `references/stages.md` — **read it before running the
stages.** The essentials:

**0 · Intake → `spec.md`.** Capture: the **audience** (which execs, what they
care about, what decision this demo is meant to move); the **demo narrative**
(the script — see above); the **data story** (what realistic sample data the
demo needs to be credible); the **demo context** (live click-through?
projected? screen-shared? self-driven by the exec? target resolution, network
conditions); the **deadline**; explicit **non-goals** (usually: auth, scale,
real integrations); constraints and references; and a **taste north-star** —
named products whose visual bar this is held to. Ask (batched) if anything
material is missing.

**1 · Plan → `plan.md`.** Phase along the demo path: the **money moment** gets
built earliest it can stand, then the narrative spine around it, then the
polish and off-script resilience passes. Every phase is demoable — you can
only judge wow on something a person can see. Use **spike phases** with a
go/no-go gate for load-bearing unknowns (an unproven API, a feasibility
question). For each phase: scope (in/out and what gets *stubbed*),
deliverable, dependencies.

**2 · Pre-mortem → `premortem.md`.** Acting as the critic: *"The demo just
happened and it flopped. Why?"* Attack across the demo-day axes — crash on the
path, generic template look, incredible data, latency during screen share,
off-script click into a dead end, the question you can't answer, the money
moment that doesn't land, scope too big for the deadline. Revise `plan.md` to
defuse the material risks. Converge, don't just count: stop when a pass finds
no new material risk; cap at ~3 passes.

**3 · Rubrics → `rubric.md`.** The keystone. *Before any code*, one rubric per
phase in the **hybrid format** (`references/rubric-format.md`): binary
**must-pass gates** (demo path runs clean, no placeholder text visible, no
console errors on the path, works at presentation resolution, design-spec
conformance if one exists) plus weighted **quality scores with floors** —
first impression / wow, narrative flow, data credibility, polish, perceived
performance. Every item names its **evidence**. Include the panel question:
*"Would <the target audience + taste north-star> lean forward at this?"* Keep
the **holistic veto** — passes every box but looks like a template → FAIL.

→ **HUMAN CHECKPOINT.** Present narrative + `plan.md` + `rubric.md` before any
code. Highest-leverage moment to course-correct.

**4 · Build.** Implement the phase to its deliverable. Build to the rubric,
not around it. Follow the stubbing doctrine: everything on the path is real;
everything visible off the path is handsomely stubbed or hidden; nothing
visible looks broken. Seed data per the data story — never random, never
placeholder.

**5 · Critique gate → `phase-<n>-eval.md`.** As the critic: **run the thing**,
gather evidence (screenshots of every state on the path, timings, console
logs), score every rubric item. Wow and credibility cannot be judged from
source — if you truly can't run it, stamp those scores **low-confidence** and
ask the human rather than asserting a pass. **Re-walk the demo path built so
far** as the regression check — a phase that breaks an earlier beat fails.
Emit a specific defect checklist and a verdict. Borderline weighted scores
(within ~0.2 of threshold) are a tie, not a pass.

**6 · Remediate.** Fix the defect list, re-score (append the round). Bounds:
**≤3 rounds**, **early-exit** when a round doesn't improve the score,
**escalate to the human** if still failing — never loop forever or silently
advance.

→ **HUMAN CHECKPOINT.** On pass, get sign-off before the next phase (default
on; see the config flag). On escalation, hand over the eval, defects, and your
recommendation.

**7 · Dress rehearsal → `rehearsal.md`.** Green phases are not a working demo.
Perform the **entire narrative end to end** — cold start, seeded data, target
resolution, timed — as the critic. Then go **off-script**: click what a
curious exec would click; nothing may visibly dead-end. Answer the panel
question for the whole demo; apply the holistic veto with teeth. A fail is an
escalation with a recommendation, never a silent ship.

**8 · Retro → `retro.md`.** Reflect on the *methodology*: which rubric items
never discriminated, what the pre-mortem missed, where the harness slowed you
down for no gain. This is how the harness improves across demos.

## Demo data doctrine

Sample data is a first-class deliverable, specified in the spec's data story:

- **Domain-true:** the audience's vocabulary, entity names, and metrics — a
  logistics exec sees lanes and carriers, not `item_1`.
- **Plausible magnitudes:** revenue, counts, and dates an insider wouldn't
  blink at. Internally consistent (totals add up, trends make sense).
- **Narrative-loaded:** the data contains the story — the anomaly the demo
  finds, the trend the chart reveals — placed where the script needs it.
- **Seeded, not random:** the same demo every run. A rehearsed demo with
  shifting data is an unrehearsed demo.

## Human-in-the-loop

Two kinds of pause — full policy in `references/hitl.md`:

1. **Clarification (anytime).** The moment audience, narrative, or a
   requirement is genuinely ambiguous, stop and ask — **batched**, with
   concrete options and a recommendation. Don't invent a requirement; a wrong
   assumption compounds across phases.
2. **Gate approval (at boundaries).** (a) after plan + rubrics before any
   code; (b) at every phase gate — governed by **`phase_gate_signoff`**
   (default **on**; `lite` sets it off); (c) on escalation; (d) at the dress
   rehearsal. Record the flag in `spec.md`.

## Failure modes to design against

- **Template face** — technically fine, visually generic; the audience has
  seen this admin dashboard before. Guard: the taste north-star, the wow
  dimension, and the holistic veto.
- **Rubric-gaming** — passing the letter of every box while the money moment
  underwhelms. Guard: the panel question and the veto.
- **Phase-local blindness** — every slice passes, the performed demo doesn't
  flow. Guard: the demo-path regression check and the dress rehearsal.
- **Production creep** — burning the deadline on auth, tests, and edge cases
  the room will never see. Guard: non-goals in the spec; the plan stubs
  aggressively.
- **Over-planning paralysis** — pre-mortem rounds with diminishing returns
  while the meeting date approaches. Guard: the convergence rule.

## Anti-patterns

- Writing the rubric *after* building the phase. The bar precedes the work.
- Letting the builder judge its own work with no role separation.
- Scoring wow/credibility from source code and reporting it as confident.
- Placeholder anything — lorem ipsum, `user_123`, gray-box images — visible in
  any state the audience can reach.
- Random or regenerated demo data that changes between rehearsal and demo.
- Building features off the narrative "while we're at it".
- Treating "max 3 rounds" as "always 3 rounds" — early-exit on no improvement.
- Silently advancing past a failing phase, or looping one forever.
- Shipping on green phases without performing the full rehearsal.
- Reading a near-threshold weighted score as a pass — the borderline band is a
  tie.
