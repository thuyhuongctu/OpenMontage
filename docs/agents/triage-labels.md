# Triage labels

The five canonical triage roles, used by the `triage` skill. OpenMontage keeps the default
vocabulary — each label string equals its role name.

| Role | Label | Meaning |
|---|---|---|
| Needs triage | `needs-triage` | Untouched. No one has decided what this is yet. |
| Needs info | `needs-info` | Blocked on the reporter. Cannot proceed without an answer. |
| Ready for agent | `ready-for-agent` | Brief is complete enough for an agent to implement unattended. |
| Ready for human | `ready-for-human` | Needs a human's judgement, credentials, or hardware. |
| Won't fix | `wontfix` | Decided against, with the reason recorded on the issue. |

These labels may not exist in the repo yet. Create a label on first use rather than silently
skipping it, and don't invent variants (`bug:triage`, `status/needs-info`) — if maintainers later
adopt other names, record the mapping here instead.

## OpenMontage-specific triage notes

Two categories recur here and are worth calling out when writing a brief:

- **Provider/API failures.** "Tool X is broken" is very often a missing API key, an exhausted
  quota, or a provider-side change — not a bug in OpenMontage. Before labelling
  `ready-for-agent`, check the reporter ran preflight (`make preflight`) and include its relevant
  output. Otherwise the label is `needs-info`.
- **Render-environment failures.** Remotion and HyperFrames both depend on Node, `npx`, and
  `ffmpeg`. Ask for `make hyperframes-doctor` output before treating a render failure as a code
  bug.

An issue that cannot be reproduced without paid provider credentials is `ready-for-human`, not
`ready-for-agent`, however clear the brief is.

## Rejected requests: `.out-of-scope/`

The `triage` skill keeps a persistent record of *rejected* feature requests in a `.out-of-scope/`
directory at the repo root, one Markdown file per rejected concept, so the same request isn't
re-litigated every time it's filed. See `.agents/skills/triage/OUT-OF-SCOPE.md` for the file
format.

That directory does not exist here yet — `triage` creates it on the first `wontfix`. Two rules
worth keeping in mind for OpenMontage specifically:

- "Already implemented" is **not** out-of-scope. Point at the tool or pipeline that already does
  it and close; the registry (`make preflight`) is how you check.
- A provider OpenMontage deliberately doesn't wrap *is* a good `.out-of-scope/` entry — record
  the reason (licensing, no API, quality, cost) so the next request gets the same answer.
