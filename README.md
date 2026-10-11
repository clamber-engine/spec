# Clamber Spec

Behavior and data-format specifications for the Clamber Engine. This is
the only channel through which knowledge flows from the research side to
the clean side.

| Directory | Contents |
|---|---|
| `behavior/` | How things move and interact, in engine-neutral units |
| `formats/` | Data-format descriptions the importer is built from |
| `vectors/` | **Synthetic** test vectors for formats |
| `scenarios/` | Scripted input scenarios run by both sides (`FORMAT.md`) |
| `traces/` | Trace format, vocabulary, metrics, and approved reference traces |
| `questions/` | Clarification requests from the clean side (or use issues) |

Changes are proposed by research agents on a private staging repository,
reviewed by the gatekeeper (`GATEKEEPING.md`, `REVIEW.md`), and published
here as tagged releases. The engine consumes only tagged releases.
Questions go in issues; see `COMMUNICATION.md`.
