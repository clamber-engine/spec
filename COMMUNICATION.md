# Communication between agents

Agents talk to each other through GitHub issues. Issues are asynchronous,
logged, and visible to the director, which makes them a record of what
crossed the firewall and when.

## Who can say what, where

| From → to | Where | Allowed content |
|---|---|---|
| Clean → research | Issue on `spec`, using a template | Anything; the clean side knows nothing tainted |
| Research → clean | A spec change, through the gatekeeper | The actual answer |
| Research → issue thread | Comment on a `spec` issue | **Status only** (see below) |
| Clean ↔ clean | Issues on `engine` | Anything |
| Any agent → director | `needs-director` label | A decision only the director can make |

### Research agents: status-only comments

On `spec` issues, research agents may only post comments like:

- "Investigating."
- "Answered in spec-staging; will appear in the next release."
- "Can't be determined by observation; proposing a behavior instead in the
  next release."
- "Duplicate of #N."

Never: numbers, frame counts, addresses, offsets, names from the original,
descriptions of how the original program works, or anything a clean agent
could use without it passing through `REVIEW.md`. The answer always goes
into a spec change.

A workflow (`.github/workflows/comment-firewall.yml`) checks every comment
and issue from `clamber-research-bot`. If it finds code-shaped content it
deletes the comment, posts a neutral notice, and labels the issue
`firewall-removed`. The research agent's own copy of everything it posts is
kept in the `research` repo.

## Issue flow on `spec`

1. A clean agent opens an issue from a template: **Question**,
   **Correction**, or **Research request**. The template applies
   `needs-research`.
2. A research agent claims it: assigns itself, swaps `needs-research` for
   `in-research`, comments "Investigating."
3. It opens a pull request on `spec-staging` that answers the issue and
   references it by URL, swaps the label to `in-review`, and comments that
   the answer is with the gatekeeper.
4. The gatekeeper merges and publishes a release whose changelog lists the
   issue, then closes it with "Released in spec-vX.Y.Z."
5. If the gatekeeper rejects the change, it tells the research agent on the
   staging pull request, never on the `spec` issue.

## Issue flow on `engine`

- Issues labeled `ready` are available. An agent claims one by assigning
  itself and adding `in-progress`, and closes it from a commit
  (`Fixes #N`).
- If work needs a spec answer, the agent opens a `spec` issue, links it,
  and labels the engine issue `blocked-on-spec`. When a release answers
  it, the agent bumps the `spec` submodule and continues.
- An agent that hits a decision outside its brief (scope, design
  direction, anything legal) labels the issue `needs-director`, explains
  the options in a comment, and moves on to other work.

## The director's inbox

The director only needs to watch issues labeled `needs-director` in
`engine` and `spec`. `firewall-removed` events are audited periodically;
the deleted content is never shown on the clean side.
