# Scenario format (v1 and v2)

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

# Version 2: structured geometry and start

Version 2 adds two sections, `[geometry]` and `[start]`, so a runner can
build the test situation from the file alone, with no prose to interpret
and no input prefix to reach the starting state. Everything else
(`[inputs]`, `[metrics]`, comments, traces) is as in version 1.

A file is v1 or v2 by its first line, which must be exactly
`# clamber-scenario v1` or `# clamber-scenario v2`. v1 files stay valid
and are read under the v1 rules above. A runner may refuse v2 files; it
must say so rather than treat one as v1.

## Example

```
# clamber-scenario v2
id: drop-after-walk
summary: Walk right off a 48 px ledge and land on flat ground.
requires: a plain edge ahead and flat ground 48 px lower
frames: 90

[geometry]
x 0.25
floor 0..39    0
floor 40..199  48

[start]
mode: grounded
facing: R
lead: first
idle_frames: 30

[inputs]
0-89   R

[metrics]
final_x   tol 1
```

## Header keys in v2

Same as v1, plus the optional `probe: yes` (see Probes), except `start` must not appear (the `[start]` section
replaces it) and `requires` is optional: if present it is one line of
plain words for people and is never read by a runner. The two new
sections are required, in the order `[geometry]`, `[start]`, `[inputs]`,
`[metrics]` (metrics still optional).

## `[geometry]`

One statement per line. All distances are px. The ground is described
in **columns** 1 px wide, numbered from 0; column 0 is the one that
contains the player's x on frame 0. A **range** is
written `C0..C1` (two dots, both ends inclusive, either end may be
negative: `-4..9`, `-9..-2`); a single column is written `C`. Heights
and other vertical positions are measured **down from level 0**
(down positive, as in traces, so a ledge below the start is positive, a
ceiling above it negative). Level 0 is the floor in column 0: the
player's base is on it on frame 0, except with `mode: airborne`, where
the player's base is `height` px above it on frame 0 (so row 0's `y`,
which is always 0, is the player's own frame-0 position, and level 0 is
`height` px below that).

| Statement | Meaning |
|---|---|
| `x OFFSET` | Once, optional (default 0). How far past the left edge of column 0 the player's x is on frame 0 (0 up to, not including, 1) |
| `floor C0..C1 H` | Columns `C0` to `C1` inclusive have their top at `H`. `C` is an integer. `H` is an integer, or `none` for no ground (a pit with no bottom) |
| `wall C0..C1 T B` | Solid block over columns `C0` to `C1`, from vertical position `T` (top) to `B` (bottom), solid on every side (`../behavior/air-walls.md`) |
| `platform C0..C1 H` | A surface at `H` over columns `C0` to `C1` that can be passed through from below and stood on from above (`../behavior/platforms-from-below.md`) |
| `ceiling C0..C1 H` | Columns have a ceiling whose underside is at `H` |
| `water C0..C1 H` | Columns hold water whose surface is at `H` (`../behavior/swimming.md`) |
| `entity KIND C H [facing L\|R]` | An object of the given kind, standing with its base at height `H` in column `C`. `KIND` is the plain name used by the behavior files: `walker`, `thrower`, `critter`, `crate`, `rope`, `launch_barrel`, `metal_barrel`, `collectible`, `sign`. New kinds are added to this list in the pull request that needs them |

Rules:

- `floor` statements must not overlap. The columns they cover must be
  contiguous, so every column from the lowest-numbered to the
  highest-numbered covered one has exactly one `floor` statement. The
  player's own column (0) must be covered, and the floor there must be
  at `H` 0 (that is what level 0 is).
- Beyond the covered columns, the floor continues flat at the height of
  the nearest covered column. Nothing else (wall, ceiling, water,
  entity) exists beyond what is stated: no other objects, no walls, no
  ceilings, no water, nothing to pick up. A runner that cannot build
  that, for example because its terrain has an extra object there,
  cannot run the scenario and says so.
- Statements may use negative column numbers for ground to the left of
  the player.
- A slope is written as a run of columns with changing heights, one
  `floor` statement per height step (compare `slope-dip` above, whose
  `requires:` line is the same description in words).
- A column can be covered by a `wall` as well as by a `floor`; the floor
  is then the surface under the block.
- The statements are exact for the columns the run can reach (the
  frames and inputs set the reach); what lies beyond that reach does
  not matter. Each side builds the situation its own way: the engine
  side a test level, the research side a setup method of its choosing.

## `[start]`

`key: value` lines, each key at most once. Keys not listed are an error.
A key left out takes its default.

| Key | Values | Default | Meaning |
|---|---|---|---|
| `mode` | `grounded`, `airborne`, `riding`, `carrying`, `swimming`, `rope` | `grounded` | The situation on frame 0 |
| `facing` | `L`, `R` | `R` | Facing direction |
| `vx`, `vy` | decimal px per frame | 0 | Velocities on frame 0. `vx` is only for `airborne` and `riding`; on the ground a runner starts from the stated `state` |
| `state` | a name from `../traces/VOCABULARY.md` | by `mode`: `grounded` `idle`; `airborne` `jump` if `vy` is negative, else `fall`; `swimming` `swim`; `rope` `rope`; `riding` `walk` if `vx` is not 0, else `idle`; `carrying` `idle` | The state recorded on frame 0 |
| `height` | px above the floor | 0 | Only with `airborne`: how far above the ground below the player is on frame 0 |
| `lead` | `first`, `second` | `first` | Which character leads |
| `follower` | `none`, `settled`, or `at DX DY` | `settled` | The other character: absent, standing close behind as it does after waiting, or at the given offset from the lead (px, right and down positive) |
| `idle_frames` | integer | 30 | With `grounded` and `state: idle`: the player has been idle for at least this many frames, so any wait-based behavior has finished |
| `held` | a button string as in `[inputs]` | none | Inputs held on the frame before frame 0 |
| `with` | `KIND` | none | Required with `riding` and `carrying`, not allowed otherwise: the kind ridden or carried |

`mode: riding` and `rope` need an `entity` of the matching kind
(`with`'s kind, or `rope`) in column 0, and `swimming` needs `water`
over column 0 reaching the player; a scenario that asks for one without
it is an error. `mode: carrying` takes no `entity` statement for the
carried object: `with` names its kind and a runner provides it, held by
the player.

Frame 0 of the trace is the state described here after zero frames of
play: row 0 shows `x`, `y` as 0 and `state` as stated (default as
above). Inputs in `[inputs]` start acting on frame 0, as in v1.

## Probes

Some starts are ones the original never produces in play (for example
a velocity it never gives). A scenario with such a start carries the
header key `probe: yes`. It is then a probe: no reference trace is
recorded from play, and it is not compared against one. Without the
key, the scenario's start is one that occurs in play and a reference
trace is expected. In every case the recorded trace is the original's
behavior from frame 0 on; however the start was reached is not part of
it.

## Test vectors

`../vectors/scenario-v2/` holds synthetic files for a parser: valid ones
with the expected parse, and invalid ones with the reason they are
rejected.
