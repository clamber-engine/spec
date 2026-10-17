# Scenario format (v1)

A scenario is a short, scripted piece of play: a starting situation, a
sequence of inputs, and the metrics to compare. The research harness and
the engine both run every scenario and write a trace (`../traces/FORMAT.md`).

Files live in this directory as `<id>.scn`. The format is line-based so a
small Lua script can parse it without libraries.

## Example

```
# clamber-scenario v1
id: run-jump-flat
summary: Run right on flat ground, then a full-height jump.
requires: flat ground, at least 360 px clear ahead, no entities within 400 px
start: grounded, idle for at least 30 frames, facing right, lead character
frames: 120

[inputs]
0-119    R
20-119   A
60-79    J

[metrics]
apex_height      tol 0.5
airtime          tol 1
top_speed        tol 0.0625
frames_to_top    tol 2
curve y,vy from jump_start for 40 tol 0.25
```

## Header

The first line must be exactly `# clamber-scenario v1`. A file without
it, or with another version, is rejected. It is the only `#` line with a
meaning; all others are comments.

Then `key: value` lines, until the first section. Every key in the table
below must appear once. A key not in the table, or a repeated key, is an
error (this catches typos such as `frame:`).

| Key | Required | Meaning |
|---|---|---|
| `id` | yes | Matches the file name, `[a-z0-9-]+` |
| `summary` | yes | One line, what the scenario exercises |
| `requires` | yes | Geometry and surroundings needed, in plain words. The research side picks a place in the original that fits; the engine side uses a test level that fits |
| `start` | yes | Player state when frame 0 begins |
| `frames` | yes | Number of logic frames to record |

## `[inputs]`

One line per held input: `FRAMES  BUTTONS`.

- `FRAMES` is a single frame (`60`) or an inclusive range (`60-79`).
- `BUTTONS` is one or more of the neutral actions below, without
  separators, in any order (`AJ` and `JA` are the same). Listing the same
  action twice on one line (`RR`) is an error. Traces still write the
  `input` column in the fixed order `LRUDJASPT`.
- Every frame a line covers must be below `frames` (0 to `frames` − 1).
  A line covering a frame at or past `frames` is an error.
- Lines combine: an action is held on a frame if any line covers it.
- Frames not covered by any line have nothing held.

| Code | Action |
|---|---|
| `L` `R` `U` `D` | Directions |
| `J` | Jump |
| `A` | Action (run while held, roll on press) |
| `S` | Swap lead character |
| `P` | Pause |
| `T` | Tandem: acts on the pair of characters; what it does in each title is in `../behavior/controls.md` |

How these map to a controller is each side's own business; each title's
own default layout is in `../behavior/controls.md`.

## `[metrics]`

One comparison per line. Metric names and definitions are in
`../traces/METRICS.md`.

- `NAME tol T`: the metric must be within ±T of the reference.
- `curve FIELDS from EVENT for N tol T`: align both traces on the first
  `EVENT`, then compare each listed field on each of the next `N` frames
  within ±T.

A scenario with no `[metrics]` section is compared on nothing; it only
records traces.

## Comments and whitespace

`#` starts a comment anywhere on a line. Blank lines are ignored. Fields
are separated by one or more spaces or tabs.
