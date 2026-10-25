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

### The second character under control

When the second character is the one under control, the stinger hurts
it by the same rule (I and A taken from its own position, the hit on
**F + 1**), but over a clearly smaller area than the first character's.
The ranges, measured in steady poses (the pose names are those of
`ball-contact.md`, except "moving", which is defined here: walking,
running, rolling and being in the air):

| Pose | I, facing right | I, facing left | A |
|---|---|---|---|
| upright | −18 to 22 | −17 to 23 | −22 to 27 |
| fidget | −18 to 22 | −17 to 23 | −22 to 27 |
| moving | −24 to 23 | −18 to 29 | −18 to 27 |
| crouching | −25 to 25 | −20 to 30 | −21 to 14 |

Facing left is the facing-right range turned around and moved in the
same way as for the first character: from 5 − (high end) to
5 − (low end). The stinger still flies through floors, still never makes
the player bounce, and is not affected by the hit.

Upright and facing right, the second character is hurt from 41
different values of I against 54 for the first: I = −19 doesn't hurt it,
where I = −21 still hurts the first character.

Measured with a setup method that put a stinger at each offset from the
player, for each placement frame of 17 pose runs (standing, turning,
walking and running both ways, rolling, jumping from standing and from a
walk or run, crouching, fidgeting), by the first frame on which the
player was hurt. The first character's upright row was measured the same
way as a control and gave the range in the table above. In each pose
the range was the same on every frame of the run, apart from the first
frames after a change of pose (turning, starting to walk, landing),
where it follows the pose the player is in on that frame.

Checked in ordinary play with the second character leading: 240 runs of
random inputs (85,503 frames) with a stinger flying on its own near the
player. The table predicted a hurt on 34 frames, and the player was hurt
on each of them, one frame later; no hurt by a stinger fell outside the
ranges. Another 23 hurts came with the stinger far away; the same inputs
without a stinger hurt the player in the same way (other objects).
These runs only cover standing, moving and crouching.

## While the player is knocked out

A stinger moves on H, stays still from **H + 1 to H + 10** (the frames
on which the player doesn't move, `knock-out.md`), and moves on by
"Moving" from **H + 11**, carrying on from where it stopped. Measured
on 54 hits. The 32-frame pause between H + 7 and H + 8 has no logic
frames.

## Pictures

What a stinger looks like on each frame in `title-1`. Each picture has a
label used in this file and is named by its picture hash
(`../formats/sprites.md`, "Picture hashes"); it is drawn at the stinger's
position as `../formats/sprites.md` says ("Where the picture is drawn").
The picture given for a frame is the one on screen with that frame's view.

| Picture | Picture hash (SHA-256) |
|---|---|
| `wing-1` | `24d164aedac3fff8935db979948dd946d86bfe1d0ec85a13af8efde6eefb8c98` |
| `wing-2` | `0bf7bf72cbf1d7148418a652f16fd166187e3f1b3edb60a3ae0ffd25e1b7c477` |
| `wing-3` | `27862880d8a4cec808a45611b92afcbcfecb2641d3b57a76ffeee9eb0aa1d643` |
| `wing-4` | `78fca5c9198911446e3bb5fcab9655b5e017a58e43b9a95cb7f5b4726e020161` |

**When a hash isn't found.** The importer looks for every hash in this
section. If any of the four isn't found, the engine shows its own art for
the stinger on every frame, keeping the timing and mirroring given here.

A stinger is always drawn mirrored left-right, whichever way it moves
and on whichever side of it the player is.

Its pictures step on **counted frames**: every frame it is in the level
except H + 1 to H + 10 of a knock-out of the player ("While the player is
knocked out"), on which it keeps the picture it had. Counted from the
frame `A` on which it appeared (`i` = 0 on `A`), its pictures repeat every
14 counted frames, from `i` = 0:

- `wing-1` × 2 (`i` = 0 and 1);
- then, repeating: `wing-2` × 2, `wing-3` × 2, `wing-4` × 3, `wing-3` × 2,
  `wing-2` × 2, `wing-1` × 3 (14 frames).

The pictures don't depend on the stinger's settings, its speed or where
it is. A stinger that was removed and comes back (`Appearing and being
removed`) starts the list again from its new `A`.

Measured by the pictures on screen: 14 different settings, 60 runs
(standing still, walking away and back so that stingers were removed and
came back, walking into the stinger, and jumping near it), 42 000
frames, 34 knock-outs. In 16 800 frames the stinger was drawn whole on
screen and recognisable, every one showed the picture above. 4 more
frames showed another picture, each with another object drawn over part
of the stinger. On the frames on which it was not wholly inside the
screen, or other things covered it, it was not counted.

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
- What defeats a stinger other than a metal barrel rolling right against
  one that doesn't move (`metal-barrel.md`, "Stingers"; other thrown
  objects are not covered), the ground action near a stinger, and
  stingers touching other objects.
- Poses not listed in `ball-contact.md`: bouncing, carrying an object,
  being hurt, swapping characters; the ground action after its first
  part, the first frame of crouching, and the air frames of running off
  an edge (all seen in the random runs, not classified); two
  characters at once, and protection (`protection.md`). For the second
  character under control, the ground action, the frames of a hop, and
  the first frames after a change of pose (the ranges follow the pose
  seen, but no ordinary-play run was checked against the table for them).
  The second character's table is otherwise checked against ordinary
  play only for standing, moving and crouching.
- Contact on the frame a stinger appears.
- Stingers far above or below the view, and walls.
