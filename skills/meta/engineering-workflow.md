# Engineering Workflow Skills — Meta Skill

## When to Use

When the work is **on OpenMontage itself**, not on a video: adding or fixing a tool, changing a
pipeline, refactoring `lib/`, writing tests, updating docs, triaging an issue, reviewing a PR,
planning a large change.

Read this file before invoking any of the vendored engineering skills. It says which one fits,
and — more importantly — where they must yield to OpenMontage's own contract.

**Do NOT use these skills for video production.** A request to make, edit, or repurpose a video
goes through the pipeline system, always. See Rule Zero in `AGENT_GUIDE.md`.

## The skills

Vendored from https://github.com/mattpocock/skills (MIT). Files live in `.agents/skills/<name>/`;
the stable 27 are symlinked into `.claude/skills/` for discovery. Provenance, the full inventory,
and what was deliberately left out: `docs/vendored-skills/mattpocock-skills.md`.

### Routing

| You are… | Skill | Notes |
|---|---|---|
| unsure which flow fits | `ask-matt` | Router over the family. |
| gathering facts before deciding | `research` | Writes findings to a Markdown file in-repo. |
| turning a discussion into a written spec | `to-spec` | Publishes to the tracker. No interview. |
| splitting a plan into work items | `to-tickets` | Tracer-bullet tickets with blocking edges. |
| planning something too big for one session | `wayfinder` | Map of decision tickets, resolved one at a time. |
| stress-testing a design before building | `grilling` / `grill-me` / `grill-with-docs` | `grill-with-docs` also emits ADRs + glossary. |
| answering a design question by building | `prototype` | Throwaway. Delete it after. |
| building from a spec or tickets | `implement` | |
| building test-first | `tdd` | |
| chasing a bug or a slowdown | `diagnosing-bugs` | |
| reviewing changes | `code-review` | Two axes: repo Standards, originating Spec. See collision note below. |
| shaping a module's interface | `codebase-design` | Deep-module vocabulary. |
| hunting refactor targets across the repo | `improve-codebase-architecture` | Emits an HTML report. |
| naming things / recording a decision | `domain-modeling` | Writes `PROJECT_CONTEXT.md` terms and `docs/adr/`. |
| mid-merge, conflicted | `resolving-merge-conflicts` | |
| writing steps only a human can do | `wizard` | Generates an interactive bash wizard. |
| out of context, handing over | `handoff` | See precedence rule 3 — not a substitute for a checkpoint. |
| writing a skill or agent doc | `writing-for-agents` | Read this before editing any skill, `AGENT_GUIDE.md`, or `CLAUDE.md`. |
| explaining a concept to the user | `teach` | |
| stuck on a decision that isn't yours | `to-questionnaire` | |
| aware your last message didn't land | `wait-what` | Re-pitch it. |
| hardening a contributor's local setup | `git-guardrails-claude-code`, `setup-pre-commit` | Opt-in, per-machine. See caveats below. |

Per-repo configuration these skills read — issue tracker, triage labels, domain doc layout —
lives in `docs/agents/`. It is already written; `setup-matt-pocock-skills` does not need to run
again unless the tracker changes.

## Precedence — where these skills yield

These are general software-engineering skills. OpenMontage's contract outranks them every time.

1. **Rule Zero wins.** Any video production request goes through `pipeline_defs/` + the stage
   director skills. `implement`, `prototype`, and `tdd` are not a path to "just write a script
   that calls the API". The capability-extension protocol
   (`skills/meta/capability-extension.md`) is the sanctioned route when a production need has no
   tool behind it.

2. **Inside a pipeline run, stage director skills win.** The name overlaps are traps:

   | Overlap | Inside a pipeline run, use |
   |---|---|
   | `research` skill vs the `research` **stage** | the pipeline's `research-director.md` |
   | `implement` skill vs the `assets` / `edit` stages | that stage's director skill |
   | `code-review` skill vs post-stage self-review | `skills/meta/reviewer.md` |
   | `handoff` skill vs resuming a run | `skills/meta/checkpoint-protocol.md` |

   A vendored skill may be used *alongside* a stage (e.g. `research` to check a provider's API
   docs mid-run), never *instead of* one.

3. **The decision communication contract still applies.** These skills say nothing about
   announcing providers, presenting both composition runtimes, or appending to `decision_log` —
   those obligations don't lapse because you're following a different skill. Same for
   checkpoints and human approval.

4. **`code-review` is an ambiguous name.** Claude Code bundles a `code-review` skill (diff
   review for correctness and cleanups); the vendored one reviews against documented Standards
   and the originating Spec. They answer different questions and resolution of the bare name
   depends on the session. When you specifically want the vendored one, read
   `.agents/skills/code-review/SKILL.md` directly.

## OpenMontage adaptations

The vendored skills were written for TypeScript/Node repos. OpenMontage is primarily Python.
Translate as follows:

| Skill assumes | Here it is |
|---|---|
| `vitest` / `npm test` | `pytest` — `make test` (all), `make test-contracts` (tool contracts) |
| `tsc --noEmit` typecheck gate | `make lint` (py_compile on core modules) |
| Prettier / ESLint | no Python formatter is enforced; match surrounding style |
| `src/` | `tools/`, `lib/`, `skills/`, `pipeline_defs/` |
| `CONTEXT.md` | `PROJECT_CONTEXT.md` |
| "tests through entrypoints" | test a tool through its `execute()` contract, not its private helpers — see `tests/contracts/` |

Node lives in `remotion-composer/` and `backlot/` only. A skill's npm-flavoured advice applies
inside those directories and nowhere else.

Two more repo-specific rules that override generic engineering instinct:

- **The tool registry is the source of truth.** Never add a hardcoded tool list to code, a
  manifest, or a doc — query `tools/tool_registry.py`. A refactor that introduces a static list
  is a regression even if every test passes.
- **New tools declare, they don't register.** Drop a `BaseTool` subclass in the right capability
  folder with `capability`, `provider`, and `agent_skills[]` set; discovery is automatic. See
  "Adding New Tools" in `skills/INDEX.md`.

### Caveats on the two setup skills

- `setup-pre-commit` installs Husky + lint-staged, which is Node tooling. Scoping it to the whole
  repo would put a Prettier pass over Python and Markdown files that nothing else formats. Only
  run it scoped to `remotion-composer/` or `backlot/`, and only with maintainer agreement.
- `git-guardrails-claude-code` writes Claude Code hooks into local settings. It is a per-machine
  contributor preference, not a repo standard — don't commit its output as project settings
  without asking.

## In-progress skills

Six upstream skills flagged unfinished are vendored but deliberately **not** symlinked into
`.claude/skills/`, so nothing auto-invokes them: `claude-handoff`, `loop-me`,
`setup-ts-deep-modules`, `writing-beats`, `writing-fragments`, `writing-shape`. Read them by
path if you want them.

The `writing-*` trio (fragments → shape → beats) is the one worth mining: it is a writing process
that overlaps with script and narration work. If it proves useful there, the right outcome is a
new Layer 2 skill under `skills/creative/`, not a promotion of the in-progress files.
