# Trace format (v1)

A trace records one run of a scenario, one row per logic frame. Reference
traces come from the original game through the research harness and live
in `reference/<scenario-id>.trace` once a gatekeeper has approved them.
The engine writes traces in the same format.

## Example

```
# clamber-trace v1
# scenario: run-jump-flat
# source: reference
frame	input	x	y	vx	vy	state	ground	facing	events
0	R	0	0	0	0	idle	1	R
1	R	0.0625	0	0.0625	0	walk	1	R
60	RJA	84.5	0	2.25	0	run	1	R	jump_start
61	RJA	86.75	-5	2.25	-5	jump	0	R
```

## Header

Lines starting with `#`, before the column line.

| Line | Meaning |
|---|---|
| `# clamber-trace v1` | Required first line |
| `# scenario: <id>` | The scenario that produced it |
| `# source: reference` or `engine` | Who recorded it |

Other `# key: value` lines are allowed and ignored by the comparison
(for example `# engine-commit:`).

## Columns

Tab-separated, in this order. The column line is required and must match.

| Column | Type | Meaning |
|---|---|---|
| `frame` | integer | Logic frame number from scenario start, from 0 |
| `input` | string | Actions held this frame, in the order `LRUDJASP`; `.` if none |
| `x` | decimal | Horizontal displacement from the start position, px, right is positive |
| `y` | decimal | Vertical displacement from the start position, px, **down is positive** |
| `vx` | decimal | Horizontal velocity, px per frame |
| `vy` | decimal | Vertical velocity, px per frame, down is positive |
| `state` | name | Player state, from `VOCABULARY.md` |
| `ground` | `0`/`1` | Standing on solid ground at the end of the frame |
| `facing` | `L`/`R` | Facing direction |
| `events` | names | Comma-separated events from `VOCABULARY.md` that happened this frame; empty if none |

Values describe the state **after** the frame's update. Decimals are
written in plain notation (no exponents) with as many places as needed;
comparisons are numeric, not textual.

## Logic frames

Only frames where the game's logic advanced are recorded. If the original
dropped frames (slowdown), the harness skips the frames where nothing
updated, so frame numbers count simulation steps, not video frames. The
harness must document how it detects this in the research repo.

## Rules for reference traces

- All positions are relative to the start position. No absolute level
  coordinates, memory addresses or raw values appear in a trace.
- States and events use only names from `VOCABULARY.md`. If the original
  shows something the vocabulary doesn't cover, extend the vocabulary in
  the same pull request.
- One trace per scenario. If a scenario is re-recorded, the new trace
  replaces the old one and the change is noted in `CHANGELOG.md`.
