# Jump arc

Vertical motion of a jump, from take-off until landing, and how holding
the jump input changes it. Frames are logic frames at 60 Hz, numbered as
in a trace. `y` and `vy` are as in a trace: down is positive, `vy` in px
per frame. Measured on flat ground with the lead character.

**P** is the press frame of the jump (as in `jump-input.md`; for a jump
that starts on landing from a press made in the air, P is that earlier
press). **T** is the frame carrying `jump_start`.

## Take-off frame

On T the player has not moved yet: `y` is unchanged and `ground` is still
1, but `vy` is already **−7**. (On a slope, `y` on T follows the floor
as on any grounded frame; see `slopes.md`.)

This doesn't depend on horizontal speed (standing, walking, running), on
whether the jump starts during a roll, or on whether it starts on landing
from a press made in the air.

## Each frame after T

Until the player lands, on each frame:

1. `vy` increases by the frame's gravity, but never above **8**.
2. The player moves down by the new `vy` (up while `vy` is negative).
3. The landing rule in `landing.md` applies.

The gravity is:

- **0.28125** (9/32) if the jump input has been held on **every** frame
  from P up to and including this frame;
- **0.4375** (7/16) otherwise.

Once the input has been released, even for one frame, the gravity is
0.4375 for the rest of the jump. Pressing jump again in the air doesn't
bring back the lighter gravity. The lighter gravity doesn't stop at the
top of the arc: a player who keeps holding jump also falls with it, until
landing. The cap of 8 applies with either gravity.

Falls that don't start with a jump (`walk-off.md`) always use 0.4375,
even if jump is pressed and held during the fall.

## How long jump is held

Let **H** be the number of frames jump is held without a break, starting
with P (held on P to P + H − 1). The lighter gravity applies on frames
T + 1 to P + H − 1, so H = 1 and H = 2 give the same jump.

`apex_height` and `airtime` as defined in `traces/METRICS.md`, for a jump
from flat ground that lands on the same ground:

| H | `apex_height` (px) | `airtime` (frames) |
|---|---|---|
| 1 or 2 | 52.5 | 31 |
| 3 | 55 | 31 |
| 4 | 57.34375 | 32 |
| 5 | 59.5625 | 33 |
| 8 | 65.71875 | 35 |
| 10 | 69.3125 | 36 |
| 12 | 72.53125 | 37 |
| 15 | 76.5625 | 39 |
| 20 | 81.28125 | 41 |
| 25 | 83.46875 | 43 |
| 26 | 83.625 | 44 |
| 30 | 83.625 | 45 |
| held until landing | 83.625 | 49 |

From H = 26 on, the top of the arc doesn't get higher, but holding longer
still makes the fall slower and the jump longer.

## Held from take-off to landing

`y` relative to the take-off frame, and `vy`, after each frame:

| Frame | `y` | `vy` |
|---|---|---|
| T | 0 | −7 |
| T + 1 | −6.71875 | −6.71875 |
| T + 2 | −13.15625 | −6.4375 |
| T + 5 | −30.78125 | −5.59375 |
| T + 10 | −54.53125 | −4.1875 |
| T + 20 | −80.9375 | −1.375 |
| T + 24 | −83.625 | −0.25 |
| T + 25 | −83.59375 | 0.03125 |
| T + 30 | −79.21875 | 1.4375 |
| T + 40 | −49.375 | 4.25 |
| T + 48 | −5.25 | 6.5 |
| T + 49 | 0 | 0 |

On T + n, while the jump is still in the air, `vy` is −7 + 0.28125 × n
and `y` is −7n + 0.140625 × n(n + 1). T + 49 is the landing frame: moving by
that frame's `vy` (6.78125) would take the player 1.53125 px below the
floor, so they land and are placed on it (`landing.md`).

## Example

Scenario `run-jump-flat` presses jump on frame 60 and holds it to frame
79, so P = 60, H = 20 and T = 61. Frames 62 to 79 use 0.28125; from frame
80 on, 0.4375. `apex_height` is 81.28125 and `airtime` 41, as in the
reference trace.

## Not covered

Horizontal motion in the air (see `air-control.md`), jumps from moving
ground (jumps from 1-in-4 slopes are in `slopes.md`), jumping after
leaving a ledge, ceilings, bounces off enemies (see `bounce.md`), and the
other character.
