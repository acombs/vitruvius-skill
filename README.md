# Vitruvius Demo

A Claude Code skill — a quality-gated harness for building highly-functional,
useful, and beautiful **demo and prototype applications for executive
audiences**, when the app's job is to impress and persuade a room rather than
run in production.

This is the `vitruvius-demo` branch of the Vitruvius methodology. For
production-grade builds, use the **vitruvius** skill on the `main` branch —
same spine (bar before code, separate critic, gated phases), opposite
trade-offs.

## What it's for

A demo is judged in a 20-minute meeting by people who decide budgets. Most demo
failures come from two places: grading your own homework after the fact, and
polishing features the audience will never see while the money moment stays
rough. Vitruvius is a harness against both. Its core conviction: **the bar is
set before the work, judged by someone other than the builder, against the
running thing — not the code.**

In practice it:

1. **Captures the demo narrative** — the script of the meeting, audience,
   decision at stake, and the data story that makes it credible.
2. **Plans along the demo path** — money moment first, then the narrative
   spine, then polish and off-script resilience.
3. **Pre-mortems demo-day failure modes** — "the demo flopped, why?" — and
   hardens the plan.
4. **Writes acceptance rubrics up front**, before any code: binary gates (path
   runs clean, zero placeholders, presentation resolution) plus weighted
   wow / narrative flow / data credibility / polish scores with floors.
5. **Builds phase by phase**, each gated by a separate *critic* role scoring
   the running artifact, with a bounded remediation loop and human checkpoints.
6. **Dress-rehearses the whole performance** — cold start, full script, timed,
   off-script probing — then **retros** the methodology itself.

Use it for demos, POCs, and prototypes for C-suite, board, customer, or
investor audiences. Don't use it for production systems — its trade-offs
(happy-path depth over edge-case breadth, perceived over actual robustness)
are deliberately wrong for production. A `lite` mode exists for quick
prototypes and internal show-and-tells.

## Who was Vitruvius?

A Roman architect whose treatise *De architectura* (1st century BC) held that
good building must have **firmitas, utilitas, venustas** — be durable, useful,
and beautiful. For a demo that translates exactly: **firmitas** — it never
breaks on stage; **utilitas** — the narrative lands and the audience gets it;
**venustas** — it looks like the future in the first five seconds. A phase
advances only when all three hold.

## Layout

```
SKILL.md                     # the methodology Claude loads
references/
  ├── stages.md              # detailed per-stage playbook
  ├── rubric-format.md       # the hybrid gate/score rubric format
  └── hitl.md                # human-in-the-loop policy
assets/templates/            # on-disk artifacts the build writes to .vitruvius/
  ├── spec.md  plan.md  premortem.md  rubric.md
  ├── phase-eval.md  rehearsal.md  retro.md
```

## Using it

Install as a Claude Code skill (e.g. clone this branch into
`~/.claude/skills/vitruvius-demo`).
It triggers automatically when you ask for a demo, prototype, POC, or showcase
app — especially for an executive audience — or invoke it by name. A build's
working state lives on disk in a `.vitruvius/` directory in the target project,
so it survives context loss and doubles as an audit trail; the rehearsal
artifact ends with a demo-day runbook you can present from.
