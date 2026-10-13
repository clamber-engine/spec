# Trace format (v1)

A trace records one run of a scenario, one row per logic frame. Reference
traces come from the original game through the research harness and live
in `reference/` once a gatekeeper has approved them (see "Where reference
traces live"). The engine writes traces in the same format.

## Example

```
# clamber-trace v1
# scenario: run-jump-flat
# source: reference
# title: title-1
frame	input	x	y	vx	vy	state	ground	facing	events
0	R	0	0	0	0	idle	1	R	
1	R	0.0625	0	0.0625	0	walk	1	R	
…
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
| `# title: <id>` | The title the trace represents, as a title id from `../TITLES.md`. Required in reference traces; see below for engine traces |

A reference trace represents exactly one title: the one it was recorded
on. Its `# title:` line is required, appears once, and holds one id.

An engine trace should have a `# title:` line naming the title whose
measured values the run used (the preset matching that title). To compare
a scenario, pick the engine preset for the reference trace's title. If
both traces have a `# title:` line and the ids differ, the comparison is
not run and is reported as a title mismatch, not as a pass or a fail. An
engine trace without the line is compared as before.

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

Every row has exactly 10 fields. When no events happened, `events` is
empty and the row ends with a tab after `facing`. Writers always write
the trailing tab. Readers also accept a row of 9 fields as having empty
`events`, and reject any other field count.

The `frame` column starts at 0 on the first row and increases by exactly
1 on each following row. A trace with a gap or a different first frame
is invalid. (The example above is an excerpt; `…` marks omitted rows.)

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
- Every reference trace has a `# title:` line.
- One reference trace per scenario and title. If a scenario is
  re-recorded on a title, the new trace replaces the old one and the
  change is noted in `CHANGELOG.md`.

## Where reference traces live

| Title | Path |
|---|---|
| `title-1` | `reference/<scenario-id>.trace` |
| Any other title | `reference/<title-id>/<scenario-id>.trace`, for example `reference/title-2/roll-flat.trace` |

The `title-1` paths are the ones used before other titles had traces and
don't change. A trace's `# title:` line always names the title its path
belongs to (`title-1` for a file directly in `reference/`). A
scenario can have a reference trace for some titles and not others;
`../TITLES.md` lists which traces exist for each title.

To compare an engine run against a title, take the reference trace at
that title's path. If there is none, the scenario has no reference for
that title: the comparison is not run, and is reported as missing, not as
a pass or a fail.

The same scenario file is used for every title. The place in the
original that a trace was recorded at fits the scenario's `requires`; it
can be a different place for each title.
