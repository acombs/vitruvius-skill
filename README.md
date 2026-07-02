# Vitruvius

A Claude Code skill — a quality-gated methodology for building
**production-grade applications** (or any substantial software artifact) from
a goal and requirements, when you want it done *right* rather than fast.

Two variants share this repo: `main` (this branch) is **vitruvius**, tuned for
production builds — real users, real data, future maintainers. The
`vitruvius-demo` branch is **vitruvius-demo**, tuned for demo/prototype apps
for executive audiences — same spine, deliberately opposite trade-offs.

## What it's for

Most quality failures come from grading your own homework after the fact and
quietly lowering the bar to match what you built. Vitruvius is a harness against
that. Its core conviction: **the bar is set before the work, judged by someone
other than the builder, against the running thing — not the code.**

In practice it:

1. **Plans** the build as vertical, demoable slices.
2. **Pre-mortems** the plan — an adversarial "it shipped and failed, why?" pass —
   and hardens it.
3. **Writes acceptance rubrics up front**, before any code, with explicit
   correctness gates (tests green, no lint/type errors, no secrets, all states
   handled) and weighted taste/usability/robustness/maintainability scores.
4. **Builds phase by phase**, each one gated by a separate *critic* role that
   scores it against the rubric and the running artifact, with a bounded
   remediation loop and human checkpoints throughout.
5. **Accepts the assembled whole** against the original spec — including
   operability: someone who isn't the builder can run it — then **retros** the
   methodology itself.

Use it for deliberate, quality-critical builds where taste and usability matter.
Don't use it for throwaway scripts or tiny edits — the ceremony costs more than
it returns. (A `lite` mode exists for lower-stakes work.) For demo/prototype
builds meant to impress a room, use the `vitruvius-demo` branch instead.

## Who was Vitruvius?

Vitruvius was a Roman architect and engineer whose treatise *De architectura*
(1st century BC) held that good building must have **firmitas, utilitas,
venustas** — be durable, useful, and beautiful.

That's the skill's quality bar, made operational: the rubrics judge **robustness**
(firmitas), **usability** (utilitas), and **taste** (venustas), and a phase
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
  ├── phase-eval.md  acceptance.md  retro.md
```

## Using it

Install as a Claude Code skill (e.g. clone into `~/.claude/skills/vitruvius`).
It triggers automatically when you hand Claude a goal + requirements and ask for a
careful, "do it right" build — or invoke it by name. A build's working state lives
on disk in a `.vitruvius/` directory in the target project, so it survives context
loss and doubles as an audit trail.
