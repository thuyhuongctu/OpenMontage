# Issue tracker: GitHub

Issues and specs for OpenMontage live as GitHub issues on the repo that `git remote -v` points at.

Skills that read from or write to this file: `to-spec`, `to-tickets`, `triage`, `wayfinder`,
`code-review` (for the originating spec).

## Two access paths — pick the one your session has

**A. Local session with the `gh` CLI** (a contributor on their own machine):

- **Create**: `gh issue create --title "..." --body "..."` (heredoc for multi-line bodies)
- **Read**: `gh issue view <number> --comments`
- **List**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`
- **Comment**: `gh issue comment <number> --body "..."`
- **Label**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**: `gh issue close <number> --comment "..."`

`gh` infers the repo from the clone.

**B. Claude Code web / remote session — no `gh`, no GitHub API over curl.** Use the GitHub MCP
tools instead (load their schemas with `ToolSearch` first):

| Operation | Tool |
|---|---|
| Create / edit / close an issue | `mcp__github__issue_write` |
| Read an issue and its comments | `mcp__github__issue_read` |
| List issues | `mcp__github__list_issues` |
| Search issues | `mcp__github__search_issues` |
| Comment | `mcp__github__add_issue_comment` |
| Sub-issues (see wayfinding) | `mcp__github__sub_issue_write` |

If a skill's instructions name a `gh` command and `gh` is absent, translate to the MCP equivalent
above rather than reporting a blocker. If neither path is available, say so and fall back to
writing the artifact as a Markdown file under `.scratch/<feature>/` — but flag that it never
reached the tracker.

## Pull requests as a triage surface

**PRs as a request surface: no.** _(Set to `yes` if this repo should treat external PRs as feature
requests; `triage` reads this flag.)_

Note that OpenMontage does receive substantial external PRs, so this flag is a real choice rather
than a formality — flip it when maintainers want the PR queue running through the same triage
states as issues. See `docs/PR_REVIEW_GUIDE.md` for the review conventions that already exist.

## When a skill says "publish to the issue tracker"

Create a GitHub issue.

## When a skill says "fetch the relevant ticket"

Read the issue with its comments (path A or B above).

## Wayfinding operations

Used by `wayfinder`. The **map** is one issue; **tickets** are child issues.

- **Map**: an issue labelled `wayfinder:map` holding the Notes / Decisions-so-far / Fog body.
- **Child ticket**: linked to the map as a GitHub sub-issue. Where sub-issues aren't available,
  add the child to a task list in the map body and put `Part of #<map>` at the top of the child.
  Labels: `wayfinder:<type>` (`research` / `prototype` / `grilling` / `task`).
- **Blocking**: GitHub's native issue dependencies where enabled; otherwise a
  `Blocked by: #<n>, #<n>` line at the top of the child body. A ticket is unblocked when every
  blocker is closed.
- **Frontier query**: the map's open children, minus any with an open blocker or an assignee;
  first in map order wins.
- **Claim**: assign the ticket to the driving dev.
- **Resolve**: comment the answer, close the ticket, then append a context pointer to the map's
  Decisions-so-far.
