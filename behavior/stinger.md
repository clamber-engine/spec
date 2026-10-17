# Stingers

Titles: `title-1`

A **stinger** is a flying enemy. It sways back and forth around the point
where it was placed, horizontally and vertically, without regard to
floors. Any contact with the player hurts: it can't be bounced on or
rolled into. This file covers how a stinger moves, when it appears and is
removed, when it hurts the player, and what it does while the player is
knocked out.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `y`
are as in a trace: right and down are positive. Speeds are in px per
frame; positions and speeds are multiples of 1/256 px.

Measured in a copy of the first level's image in which one placement
near the level's start was given each of the stinger settings used by
the game's levels in turn (14 different settings), with the first
character under control and alone:

- movement: each setting for 1459 frames while the player stood still
  away from it (one setting, whose stinger reached the player, for 282
  frames through the hit), every frame matching the rules below
  exactly;
- appearing and removal: 18 runs walking away from a stinger placed high
  above the player and back, 34 appearances and 31 removals, each on the
  frame the rules give and none on any other frame;
- contact: a stinger that doesn't move (all settings 0) set next to the
  player one pixel apart, along a column and a row through the reach,
  on 17 frames: standing (9 frames), the fidget pose, rolling, early in
  a jump (upright) facing right, and turned to face left, crouching and
  airborne facing each way; and 400 runs of random walking, running,
  rolling, crouching and jumping near four moving stingers (178 301
  frames, 155 hits): every hit came on the frame the rules give and none
  on any other frame, leaving out 90 frames in poses not covered (5 of
  them followed by a hit). The same 400 runs with the stinger kept far
  away had no hits.

## Settings

A stinger is placed with (level data):

- its **start** (X0, Y0), whole pixels;
- four **reaches**, whole pixels from 0 to 255: `Rl` to the left, `Rr` to
  the right, `Ru` up and `Rd` down;
- two **start speeds** `sx0` (horizontal) and `sy0` (vertical).

How a level's stingers get their settings belongs to how they are placed
(`../formats/placements.md`) and is not covered.

## Moving

A stinger has a speed (`vx`, `vy`) and a **target speed** (`tx`, `ty`).
It heads **left** while `tx` is negative and **right** otherwise
(`tx` = 0 counts as right); it heads **down** while `ty` is positive and
**up** otherwise (`ty` = 0 counts as up). Its turning points are
X0 − `Rl` and X0 + `Rr` horizontally, Y0 − `Ru` and Y0 + `Rd` vertically.

On each frame, in this order:

1. **Horizontal turning point.** Heading left, if `x` rounded down is
   less than X0 − `Rl`, the stinger is put at `x` = X0 − `Rl` + 1/2 and
   `tx` changes sign. Heading right, if `x` rounded down is greater than
   X0 + `Rr`, it is put at `x` = X0 + `Rr` + 1/2 and `tx` changes sign.
2. **Horizontal speed.** `vx` moves toward `tx` by one 32nd of the
   difference, rounded toward zero to a multiple of 1/256. If that step
   would be 0 (the difference is less than 32/256), `vx` becomes `tx`
   exactly.
3. The stinger moves horizontally by the new `vx`.
4. **Vertical turning point.** Heading down, if `y` rounded up is
   greater than Y0 + `Rd`, the stinger is put at `y` = Y0 + `Rd` − 1/2
   and `ty` changes sign. Heading up, if `y` rounded up is less than
   Y0 − `Ru`, it is put at `y` = Y0 − `Ru` − 1/2 and `ty` changes sign.
5. The stinger moves vertically by `vy` **as it was before this frame**.
6. **Vertical speed.** `vy` then moves toward `ty` by the rule of step 2.

Note the different order: horizontally the speed changes before the
move, vertically after it.

The target speeds only ever change sign, so their size stays that of
the start speeds. Because the speed eases toward the target, a stinger
carries on well past a turning point before it comes back: the
turning points are where it turns, not how far it goes. With start
speed −2.5, no vertical speed and reaches of 50 px, a stinger swings to
about 931 and 1070 when its start is at 1000 (the first swing, which
starts at full speed, reaches 927.3). With reaches of 0 and the same
speed, the first swing reaches 977.3 and the swings then die down to
less than 2 px either side of the start.

A stinger whose start speeds are both 0 stays at its start. One whose
vertical start speed is 0 stays at the height of its start.

Floors don't stop a stinger: it moves through the ground as if it
weren't there. Walls were not tested.

## Appearing and being removed

Stingers appear and are removed by the window rule of walkers
(`walker-view.md`), with the same view position `V` and the same window
`−31 ≤ X0 − V ≤ 288`:

- A stinger that isn't in the level appears on the first frame whose `V`
  puts its start in the window.
- It is removed on a frame on which its start is outside the window and
  its `x` after that frame's move (step 3), rounded down to `X`, has
  `X − V ≤ −128` or `X − V ≥ 385`.
- A removed stinger comes back by the same rule, starting afresh.

On the frame it appears, a stinger is at its start (`x` = X0, `y` = Y0,
no fractions), with `vx` = `tx` = `sx0` and `vy` = `ty` = `sy0`. It
doesn't move on that frame. It moves by "Moving" from the next frame on.

Only horizontal positions were tested; the stingers measured were
level with the view or above it.

## Touching the player

Contact is decided from whole-pixel positions at the end of a frame, as
for balls (`ball-contact.md`, "Offsets"):

- **I** = the player's `x` rounded down minus the stinger's `x` rounded
  down;
- **A** = how many pixels the stinger is above the player: the player's
  `y` rounded up minus the stinger's `y` rounded up.

On a frame **F**, once both have moved, the player is hurt on frame
**F + 1** (the hit frame H of `knock-out.md`) when **I** and **A** are
within the ranges for the player's pose on F. Poses are those of
`ball-contact.md` ("Which pose the player has"), with the same timing.
Both ends are included:

| Pose | I, facing right | I, facing left | A |
|---|---|---|---|
| upright | −21 to 32 | −27 to 26 | −20 to 29 |
| airborne | −21 to 29 | −24 to 26 | 1 to 46 |
| rolling | −19 to 32 | −27 to 24 | −16 to 27 |
| crouching | −24 to 23 | −18 to 29 | −24 to 20 |
| fidget | −11 to 35 | −30 to 16 | −19 to 33 |

Facing left, every I range is the facing-right range turned around and
moved: from 5 − (high end) to 5 − (low end). The stinger itself never
turns, and its reach doesn't change while it flies.

These are the ranges for balls in the first part of their turn
(`ball-contact.md`, "Ranges") changed by the same amounts in every
pose: facing right, the low end of I lower by 6 and the high end higher
by 13; facing left, the low end lower by 6 and the high end higher by
13 too; the A range lower by 17 at its low end and by 8 at its high end
(where the ball table gives both ends). A stinger can hurt a
player standing on the ground from below the floor (A below 0), since it
flies through floors.

Contact with a stinger never makes the player bounce, also when the
player comes down onto it from above or rolls into it: it hurts or does
nothing. The stinger is not affected by the hit.

## While the player is knocked out

A stinger moves on H, stays still from **H + 1 to H + 10** (the frames
on which the player doesn't move, `knock-out.md`), and moves on by
"Moving" from **H + 11**, carrying on from where it stopped. Measured
on 54 hits. The 32-frame pause between H + 7 and H + 8 has no logic
frames.

## Example

Synthetic settings: start (1000, 300), `Rl` = `Rr` = 6, `Ru` = `Rd` =
3, `sx0` = −2, `sy0` = +1.5. Values at the end of each frame; frame 0 is
the frame it appears.

| Frame | `x` | `y` | `vx` | `vy` | `tx` | `ty` | |
|---|---|---|---|---|---|---|---|
| 0 | 1000 | 300 | −2 | 1.5 | −2 | 1.5 | appears, doesn't move |
| 1 | 998 | 301.5 | −2 | 1.5 | −2 | 1.5 | |
| 2 | 996 | 303 | −2 | 1.5 | −2 | 1.5 | |
| 3 | 994 | 304.5 | −2 | 1.5 | −2 | 1.5 | |
| 4 | 992 | 304 | −2 | 1.40625 | −2 | −1.5 | `y` rounded up 305 > 303: put at 302.5, moves by 1.5, then `vy` eases by 3/32 |
| 5 | 992.625 | 305.40625 | −1.875 | 1.31640625 | 2 | −1.5 | `x` rounded down 992 < 994: put at 994.5, `vx` eases by 4/32, moves |
| 6 | 990.87109375 | 306.72265625 | −1.75390625 | 1.23046875 | 2 | −1.5 | |

On frame 6, `vx` eases by (2 + 1.875)/32 = 0.12109375 (31/256 exactly), and `vy` by (1.5 + 1.31640625)/32 rounded toward zero
to 22/256 = 0.0859375. The stinger goes on to its furthest point left,
`x` 975.8984375, on frame 26, and its lowest point, `y` 318.078125, on
the same frame.

A player standing still facing right at `x` 976.5 (rounded down 976),
`y` 300 (upright): on frame 1, I = 976 − 998 = −22, outside −21 to 32;
on frame 2, I = 976 − 996 = −20 and A = 300 − 303 = −3 (the stinger's
`y` 303): both within reach, so the player is hurt on frame 3.

## Not covered

- How a level's stingers get their settings, and stingers placed other
  than near the first level's start (this file was measured with a
  stinger placed there in a copy of the image).
- What defeats a stinger (for example thrown objects), the ground action
  near a stinger, and stingers touching other objects.
- Poses not listed in `ball-contact.md`: bouncing, carrying an object,
  being hurt, swapping characters; the ground action after its first
  part, the first frame of crouching, and the air frames of running off
  an edge (all seen in the random runs, not classified); the second
  character under control, two characters, and protection
  (`protection.md`).
- Contact on the frame a stinger appears.
- Stingers far above or below the view, and walls.
