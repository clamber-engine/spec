# Governance

## Roles

| Role | Writes to | Notes |
|---|---|---|
| Clean | `engine` | Must never have studied the original's internals |
| Research | `research` (private), `spec` via PR | May use any analysis tools |
| Gatekeeper | Approves `spec` PRs | Usually research-side; never contributes to `engine` |

Sides are permanent: research → clean is never allowed.

## Roster

| GitHub handle | Side | Since |
|---|---|---|
| elle-trudgett | Research, gatekeeper, org owner (administration only on `engine`) | 2026-10-11 |

## Information flow

`research` → `spec` (gatekeeper-reviewed, tagged) → `engine` (pinned tag).
Nothing flows back except questions, asked publicly on this repo.
No private channels about implementation between sides.

## Agents

AI agents may commit and push to `engine` and `research` without review,
within the rules in each repo's `CLAUDE.md`. Changes to `spec` always go
through a pull request approved by a gatekeeper. An agent never merges its
own spec change.

## Known limitation

There is currently one gatekeeper, who is also the main spec author.
Self-review against `REVIEW.md` is the interim process. Recruit a second
research-side gatekeeper before the repositories go public.
