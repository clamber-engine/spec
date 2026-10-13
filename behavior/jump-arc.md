# Jump arc

Titles: `title-1`, `title-2`

Vertical motion of a jump, from take-off until landing, and how holding
the jump input changes it. Frames are logic frames at 60 Hz, numbered as
in a trace. `y` and `vy` are as in a trace: down is positive, `vy` in px
per frame. Measured on flat ground with the lead character.

**P** is the press frame of the jump (as in `jump-input.md`; for a jump
that starts on landing from a press made in the air, P is that earlier
press). **T** is the frame carrying `jump_start`.

The sections below up to "In `title-2`" give the `title-1` values. The
take-off speed, both gravities, when the lighter gravity stops, and the
delay from press to take-off differ in `title-2`; "In `title-2`" gives the
complete `title-2` values.

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

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground. Speeds are whole
multiples of 1/256 px per frame, written both ways. When a jump starts is
in `jump-input.md` ("In `title-2`"): a jump starts on a grounded frame
**G**, and T is **G + 2**. For a press made on the ground, G is the press
frame P, so T = P + 2 (in `title-1`, T = P + 1).

### Take-off frame (`title-2`)

On G and G + 1 the player is still on the ground and nothing about the
jump is visible yet: `y` is unchanged and `vy` is the grounded value.
On T the player has not moved yet either (`y` unchanged, `ground` still
1), but `vy` is already **−8.21875** (−2104/256).

This doesn't depend on horizontal speed (standing, walking, running, in
either direction), on whether the jump starts during a roll or while
crouched, or on whether it starts on landing from a press made in the
air.

### Each frame after T (`title-2`)

Until the player lands, on each frame:

1. `vy` increases by the frame's gravity, but never above **8**.
2. The player moves down by the new `vy` (up while `vy` is negative).
3. If that would take the player below the floor, they land on this
   frame instead and are placed on the floor.

The gravity is:

- **0.3515625** (90/256) if the jump input has been held on **every**
  frame from G up to and including this frame, **and** `vy` before this
  frame's increase is negative (the player is still rising);
- **0.4375** (112/256) otherwise.

So, unlike `title-1`, the lighter gravity stops at the top of the arc
even if jump is still held: from the first frame that starts with `vy`
zero or positive, gravity is 0.4375 until landing. (A jump never has `vy`
exactly 0, so the frame that crosses the top always uses the lighter
gravity if jump is still held.) As in `title-1`, once the input has been
released, even for one frame, the gravity is 0.4375 for the rest of the
jump, and pressing again in the air doesn't bring the lighter gravity
back. The cap of 8 applies with either gravity.

### How long jump is held (`title-2`)

Let **H** be the number of frames jump is held without a break, starting
with G (held on G to G + H − 1). The lighter gravity applies on frames
T + 1 to G + H − 1 while the player is rising, so H = 1, 2 and 3 give the
same jump.

`apex_height` and `airtime` as defined in `traces/METRICS.md`, for a jump
from flat ground that lands on the same ground:

| H | `apex_height` (px) | `airtime` (frames) |
|---|---|---|
| 1 to 3 | 73.125 | 37 |
| 4 | 74.671875 | 37 |
| 5 | 76.2109375 | 38 |
| 6 | 77.671875 | 38 |
| 8 | 80.3359375 | 39 |
| 10 | 82.7265625 | 40 |
| 12 | 84.875 | 40 |
| 15 | 87.515625 | 41 |
| 18 | 89.578125 | 42 |
| 20 | 90.578125 | 43 |
| 22 | 91.3515625 | 43 |
| 25 | 91.9140625 | 44 |
| 26 or more, or held until landing | 92 | 44 |

From H = 26 on, holding longer changes nothing: the player is already
falling, and falling always uses 0.4375.

### Held from take-off to landing (`title-2`)

`y` relative to the take-off frame, and `vy`, after each frame:

| Frame | `y` | `vy` |
|---|---|---|
| T | 0 | −8.21875 |
| T + 1 | −7.8671875 | −7.8671875 |
| T + 2 | −15.3828125 | −7.515625 |
| T + 5 | −35.8203125 | −6.4609375 |
| T + 10 | −62.8515625 | −4.703125 |
| T + 20 | −90.546875 | −1.1875 |
| T + 23 | −92 | −0.1328125 |
| T + 24 | −91.78125 | 0.21875 |
| T + 25 | −91.125 | 0.65625 |
| T + 30 | −81.28125 | 2.84375 |
| T + 40 | −28.78125 | 7.21875 |
| T + 41 | −21.125 | 7.65625 |
| T + 42 | −13.125 | 8 |
| T + 43 | −5.125 | 8 |
| T + 44 | 0 | (lands) |

On T + n for n = 1 to 24, `vy` is −8.21875 + 0.3515625 × n. T + 24 is the
last frame with the lighter gravity (it starts with `vy` = −0.1328125).
From T + 25, `vy` grows by 0.4375 per frame and reaches the cap on T + 42.
T + 44 is the landing frame: moving by 8 would take the player 2.875 px
below the floor, so they land and are placed on it.

The landing frame's `vy` and the landing rules in `landing.md` were not
measured for `title-2`. Landing on slopes is in `slopes.md` ("In
`title-2`").

### Example (`title-2`)

Standing, jump pressed on frame 10 and held for 20 frames (frames 10 to
29): G = 10, H = 20, T = 12. Frames 13 to 29 use 0.3515625; from frame 30
on, 0.4375. `apex_height` is 90.578125 and `airtime` 43; the player lands
on frame 55.

## Not covered

Horizontal motion in the air (see `air-control.md`), jumps from moving
ground (jumps from 1-in-4 slopes are in `slopes.md`), ceilings, and bounces off enemies (see `bounce.md`). Jumping near
the edge of a ledge is in `walk-off.md`, jumping during a fall after
rolling off a ledge in `rolling-off-ledges.md`. The second character's
values are in `second-character.md`.

For `title-2`: ceilings and the second character have not been
measured. Jumps from slopes are in `slopes.md` ("In `title-2`"). Falls that don't start with a jump, and jumping
just after leaving a ledge, are in `walk-off.md` ("In `title-2`"), and
jumping during a fall after rolling off a ledge in `rolling-off-ledges.md`
("In `title-2`"). Horizontal motion in the air is in
`air-control.md` and bounces in `bounce.md` (sections "In `title-2`").
