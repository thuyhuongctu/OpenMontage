# Domain docs

How the engineering skills should consume OpenMontage's domain documentation.

**Layout: single-context.** One root context document plus ADRs.

## Before exploring, read these

- **`PROJECT_CONTEXT.md`** at the repo root — this is OpenMontage's `CONTEXT.md`. It holds the
  architecture, key files, and conventions. Where an upstream skill says "read `CONTEXT.md`",
  read `PROJECT_CONTEXT.md`.
- **`AGENT_GUIDE.md`** at the repo root — the operating contract. Non-negotiable for any agent
  touching this repo, and it outranks a skill's own suggestions where the two disagree.
- **`skills/INDEX.md`** — the map of the 3-layer knowledge architecture and every Layer 2 skill.
- **`docs/ARCHITECTURE.md`** and **`docs/adr/`** — read ADRs touching the area you're about to
  change. `docs/adr/` does not exist yet; `domain-modeling` creates it lazily when a decision
  actually needs recording. Number ADRs `0001-`, `0002-`, … .

There is no `CONTEXT-MAP.md` and no per-package `CONTEXT.md`: OpenMontage is one Python package
plus two Node sub-projects (`remotion-composer/`, `backlot/`), not a monorepo of independent
contexts. Don't create a multi-context layout without a decision to do so.

If a file listed above is missing, proceed silently. Don't flag its absence or propose creating
it upfront.

## Use the project's vocabulary

`PROJECT_CONTEXT.md` and `AGENT_GUIDE.md` define the terms that matter here — pipeline, stage,
stage director skill, artifact, checkpoint, tool registry, capability, provider, selector,
support envelope, playbook, runtime, authoring mode (templated vs atelier), Backlot board. Use
those words as defined. In particular:

- A **tool** is a `BaseTool` subclass under `tools/`, not any executable.
- A **skill** is one of three layers (see `skills/INDEX.md`); say which layer you mean.
- A **stage** is a pipeline stage from `pipeline_defs/*.yaml`, not a generic step.

If the concept you need isn't in that vocabulary, that's a signal: either you're inventing
language the project doesn't use (reconsider), or there's a real gap worth recording.

## Flag conflicts explicitly

If your output contradicts an ADR, `PROJECT_CONTEXT.md`, or a hard rule in `AGENT_GUIDE.md`,
surface it rather than quietly overriding:

> _Contradicts AGENT_GUIDE.md's "Present Both Composition Runtimes" rule, but worth reopening
> because…_

The hard rules in `AGENT_GUIDE.md` (Rule Zero, both-runtimes, decision re-logging) are the ones
most likely to be broken by a well-meaning refactor. Treat them as ADRs that happen to live in
the guide.
