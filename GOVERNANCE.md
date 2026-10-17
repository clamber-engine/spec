# Governance

Clamber is built by AI agents working on two sides of a clean-room
firewall, under the direction of one human.

## Roles

| Role | Who | GitHub account | Environment | Can see |
|---|---|---|---|---|
| Director | Human | `elle-trudgett` (org owner) | Admin environment | `engine`, released `spec`, issues |
| Research agents | Agents | `clamber-research-bot` | Research environment (has the ROM and emulator) | `research`, `spec-staging`, `spec` issues |
| Gatekeeper agent | Agent | `clamber-gatekeeper-bot` | Research environment, separate user, no ROM or research checkout | `spec-staging`, `spec` |
| Clean agents | Agents | `clamber-bot` | Clean environment | `engine`, released `spec`, `spec` issues |

**The director** sets direction, files requirements, answers `needs-director`
issues, and administers the orgs. Process changes from the director (to
`REVIEW.md`, `GATEKEEPING.md`, `COMMUNICATION.md` and this file) are opened
as pull requests on `spec-staging` from a branch based on released `spec`,
so the gatekeeper reviews and releases them like any other change; the
director still never reads `spec-staging`'s other contents. The director never reads the `research`
repo, `spec-staging`, or a research agent's working output, so that their
instructions to either side can't carry internals across. Owner access to
those repos is for administration only.

**Research agents** study the original game by any means and turn findings
into spec changes, proposed as pull requests on `clamber-research/spec-staging`.

**The gatekeeper agent** reviews every staging pull request against
`REVIEW.md`, merges what passes, publishes it to `clamber-engine/spec`, and
tags releases. It never reads the `research` repo: it judges only what a
pull request says, the same way a clean reader would see it. See
`GATEKEEPING.md`.

**Clean agents** implement the engine from engine issues and released spec
versions.

## Information flow

```
research ──► spec-staging (PR) ──gatekeeper──► spec (tagged release) ──► engine
```

Nothing flows from research to the clean side except through the
gatekeeper. Questions flow the other way as issues on `spec`; see
`COMMUNICATION.md`.

## Contributions

Every contribution to the spec (pull requests on `spec-staging` and the
releases published from them) is made under CC0 1.0, the same dedication
as the spec itself (see `LICENSE` and the License section of `README.md`).
By proposing a change, a contributor dedicates it to the public domain.

## Permanence

An account, agent configuration or human on one side never moves to the
other. Environments are never shared between sides.

## Repository access

| Team | Org | Repo access |
|---|---|---|
| `clean` | clamber-engine | `engine`: write, `spec`: read |
| `research` | clamber-engine | `spec`: triage (issues and labels only) |
| `gatekeepers` | clamber-engine | `spec`: write |
| `research` | clamber-research | `research`: write, `spec-staging`: write |
| `gatekeepers` | clamber-research | `spec-staging`: write (no access to `research`) |

Base permissions in both orgs are "No permission".

## Known limitations

- Gatekeeping is done by an agent without human approval. The director
  audits a sample of releases and every `firewall-removed` event.
- The repositories are private on GitHub's free plan, so branch protection
  and required reviews are not enforced by GitHub; the rules above are
  enforced by account permissions and agent configuration.
