# Changelog

Spec releases are tagged `spec-vMAJOR.MINOR.PATCH`. The engine pins one.

## Unreleased

- Behavior: new `behavior/landing.md`. A falling player lands on the
  first frame its motion takes it less than 5 px above the floor, and is
  placed on the floor with `vy` 0.
- Scenarios: `run-jump-flat` shortened to 120 frames and asks for 360 px
  of clear ground; first reference trace `traces/reference/run-jump-flat.trace`.
- Vocabulary: `roll_end` goes on the first frame after the roll.

## spec-v0.5.0

- Behavior: new `behavior/roll-input.md`. When an Action press starts a
  roll (any grounded press without Down; not in the air, except a late
  press held through landing), how a roll ends depending on whether Action
  stays held, and when the next roll can start. Answers spec#9.

## spec-v0.4.0

- Behavior: new `behavior/jump-input.md`. Covers jump timing on the
  ground and during a roll, a defined landing lockout (earliest jump on
  L + 3), and buffered presses held through landing. Answers spec#8.

## spec-v0.3.0

- Traces: every row has 10 fields (trailing tab when `events` is empty;
  readers also accept 9); `frame` starts at 0 with no gaps.
  Answers spec#2, spec#3.
- Metrics: a missing closing `land` makes `apex_height`, `airtime` and
  `bounce_height` missing; height windows include the `land` frame.
  Answers spec#4.
- Curves: fail if the aligning event is missing from one trace, pass if
  missing from both. Answers spec#5.
- Scenarios: the `# clamber-scenario v1` line is now **required**; unknown
  or repeated header keys and inputs at or past `frames` are errors;
  buttons may be listed in any order, repeats are errors. Answers spec#6,
  spec#7.

## spec-v0.2.0

- Process for autonomous agents: governance roles, `COMMUNICATION.md`,
  `GATEKEEPING.md`, issue templates, comment firewall.

## spec-v0.1.0

- Governance and review process.
- v1 formats for scenarios, traces, vocabulary and metrics.
- Example scenarios `run-jump-flat` and `walk-off-ledge`.
