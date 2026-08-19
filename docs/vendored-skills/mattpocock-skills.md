# Matt Pocock's Engineering Skills — Provenance

The 33 engineering/productivity workflow skills listed below are vendored into
`.agents/skills/` from Matt Pocock's public skills repo.

- **Source**: https://github.com/mattpocock/skills
- **Vendored commit**: `885e2ca4` (2026-08-19)
- **Vendored version**: `mattpocock-skills` v1.2.3
- **Vendor date**: 2026-08-19
- **License**: MIT — Copyright (c) 2026 Matt Pocock. The MIT notice must travel with any
  copy or derivative of these files.

## Why vendor instead of installing the plugin

Upstream offers two install paths: a Claude Code plugin (`claude plugins install mattpocock-skills`)
and a file copy (`npx skills@latest add mattpocock/skills`).

OpenMontage vendors, because:

1. **The plugin is per-machine, not per-repo.** It configures a contributor's Claude Code
   install; it puts nothing in the repo, so a fresh clone or a web/CI session gets nothing.
2. **OpenMontage is multi-agent.** `AGENTS.md`, `CODEX.md`, `COPILOT.md`, and `CURSOR.md` all
   route to `AGENT_GUIDE.md`. A Claude-Code-only plugin serves one of those agents. Each
   vendored skill keeps its upstream `agents/openai.yaml`, so Codex-family agents get it too.
3. **It matches existing practice.** The HyperFrames family is already vendored the same way
   (see `.agents/skills/hyperframes/PROVENANCE.md`).

The tradeoff is that updates are manual. See [Updating](#updating) below.

## What these skills are (and are not)

They are **engineering workflow** skills: how to plan, spec, ticket, implement, test, review,
debug, and document *software*. They are about building OpenMontage.

They are **not** video-production skills and they do **not** replace any part of the pipeline
system. The precedence rules live in `skills/meta/engineering-workflow.md`, which is the Layer 2
skill an agent should read before using any of them.

## What's vendored

### Engineering (18)

| Skill | Purpose |
|---|---|
| `research` | Investigate a question against primary sources; capture findings as Markdown in-repo. |
| `to-spec` | Turn the current conversation into a spec, published to the issue tracker. |
| `to-tickets` | Break a plan into tracer-bullet tickets with declared blocking edges. |
| `triage` | Move issues/external PRs through a triage state machine; write agent-ready briefs. |
| `wayfinder` | Plan work too large for one agent session as a map of decision tickets. |
| `implement` | Implement a piece of work from a spec or set of tickets. |
| `tdd` | Red-green-refactor; integration tests through entrypoints. |
| `code-review` | Review changes since a fixed point on two axes: repo Standards and originating Spec. |
| `diagnosing-bugs` | Diagnosis loop for hard bugs and performance regressions. |
| `prototype` | Throwaway prototype to answer a design question. |
| `codebase-design` | Shared vocabulary for designing deep modules and placing seams. |
| `improve-codebase-architecture` | Scan for deepening opportunities, report as HTML, then grill one. |
| `domain-modeling` | Build/sharpen the domain model; write `CONTEXT.md` and ADRs. |
| `grill-with-docs` | Relentless design interview that emits ADRs and glossary entries as it goes. |
| `resolving-merge-conflicts` | Resolve an in-progress merge/rebase conflict. |
| `wizard` | Generate an interactive bash wizard for steps only a human can perform. |
| `ask-matt` | Router over this skill family — "which flow fits my situation?". |
| `setup-matt-pocock-skills` | One-time per-repo config. **Already run** — see `docs/agents/`. |

### Productivity (7)

| Skill | Purpose |
|---|---|
| `grilling` | Stress-test a plan, decision, or idea by relentless questioning. |
| `grill-me` | The interview form of the above, sharpening a plan or design. |
| `handoff` | Compact the current conversation into a handoff document. |
| `teach` | Teach the user a concept, with mission/glossary/learning-record formats. |
| `to-questionnaire` | Turn a decision you can't answer into a questionnaire for someone else. |
| `wait-what` | "That last message did not land" — re-pitch it. |
| `writing-for-agents` | How to write docs *for agents* — skills, `AGENTS.md`, `CLAUDE.md`. |

### Misc (2)

| Skill | Purpose |
|---|---|
| `git-guardrails-claude-code` | Claude Code hooks that block destructive git commands. Ships `scripts/block-dangerous-git.sh`. |
| `setup-pre-commit` | Husky + lint-staged pre-commit hooks (Node side of the repo). |

### In progress (6) — vendored, not auto-discovered

Upstream keeps these under `skills/in-progress/` and flags them as unfinished. They are vendored
so they can be read and adapted, but they are **not** symlinked into `.claude/skills/`, so no
agent will auto-invoke them. Read them by path when you want them.

`claude-handoff`, `loop-me`, `setup-ts-deep-modules`, `writing-beats`, `writing-fragments`, `writing-shape`

The three `writing-*` skills are the interesting ones for OpenMontage: they are a
fragments → shape → beats writing process that overlaps with script and narration work. Treat
them as raw material for a future Layer 2 script-writing skill, not as production guidance.

## Intentionally NOT vendored

| Skill | Why not |
|---|---|
| `migrate-to-shoehorn` | Migrates TS test files to `@total-typescript/shoehorn`. OpenMontage's TypeScript is Remotion compositions and the Backlot server; there are no `as`-heavy TS test fixtures to migrate, and the dependency isn't used. |
| `scaffold-exercises` | Scaffolds course exercise directories (sections/problems/solutions) for Matt's own teaching repos. No analogue here. |

Upstream also has an empty `skills/deprecated/` (README only) and `.out-of-scope/` notes; neither
carries a skill.

## How discovery works

`.agents/skills/<name>/` holds the real files. `.claude/skills/<name>` is a **relative symlink**
to it (`../../.agents/skills/<name>`) for the 27 stable skills — the pattern `skills/INDEX.md`
already documents for Layer 3.

Upstream frontmatter decides how each one is reachable:

- **13 model-invocable** (no `disable-model-invocation` flag): `code-review`, `codebase-design`,
  `diagnosing-bugs`, `domain-modeling`, `git-guardrails-claude-code`, `grilling`, `prototype`,
  `research`, `resolving-merge-conflicts`, `setup-pre-commit`, `tdd`, `wizard`,
  `writing-for-agents`. An agent may reach for these on its own when the description matches.
- **14 user-invoked only** (`disable-model-invocation: true`): `ask-matt`, `grill-me`,
  `grill-with-docs`, `handoff`, `implement`, `improve-codebase-architecture`,
  `setup-matt-pocock-skills`, `teach`, `to-questionnaire`, `to-spec`, `to-tickets`, `triage`,
  `wait-what`, `wayfinder`. These run only when a human asks for them by name.

### Known name collision: `code-review`

Claude Code bundles its own `code-review` skill (diff review for correctness and cleanups).
Matt's `code-review` is a different thing: it reviews against the repo's documented **Standards**
and the originating **Spec**. They share a name.

Which one a bare `/code-review` resolves to depends on the session's skill sources, so don't
assume. To be sure you get the vendored one, read `.agents/skills/code-review/SKILL.md`
directly. Both are useful and they answer different questions — the bundled one asks "is this
code correct?", the vendored one asks "does this code follow our rules and do what the ticket
asked?".

## Updating

There is no lockfile for these (`skills-lock.json` is gitignored). To refresh:

```bash
git clone --depth 1 https://github.com/mattpocock/skills.git /tmp/mp-skills
# diff a single skill before taking it
diff -ru .agents/skills/tdd /tmp/mp-skills/skills/engineering/tdd
```

Re-vendor per skill rather than wholesale, and update the commit/version/date at the top of this
file when you do. Local edits to a vendored skill are fine — record them in a
`## OpenMontage patches` section inside that skill's `SKILL.md` so the next re-vendor doesn't
silently drop them.
