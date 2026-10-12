# Steep faces and walls

What happens when a grounded player meets ground that rises or falls too
steeply to walk on: being stopped by it, not being able to start walking
toward it from a standstill, being carried down a steep face, and leaving
the ground on one. Frames are logic frames at 60 Hz, numbered as in a
trace. `x`, `y` and `vx` are as in a trace: right and down are positive,
speeds in px per frame. Speeds are whole multiples of 1/256 px per frame
and are written both ways, as in `ground-speed.md`. "Left", "Right" and
"Action" are the neutral inputs `L`, `R`, `A` of the scenario format.

Measured with the lead character, walking, running, standing and
coasting with nothing held, on four steep faces (going down faces that
fall to the right and to the left, and against faces that rise to the
right and to the left) and against one vertical step up, going left.
Jumps, rolls and landings on or against these surfaces are not covered
(see the end).

## Columns, faces and steps

The floor has one height for each 1-px-wide column of `x`. "The
player's column" is the column containing `x` (its whole-pixel part).
"Higher" means higher on screen (smaller `y`).

- A **steep face** is a run of adjacent columns where each column's
  floor is exactly **4 px** higher than the one before it. A **face
  column** is a column whose floor differs by exactly 4 px from a
  neighbouring column's floor.
- A face **rises** in the direction its floor goes up and **falls**
  (is downhill) in the other.
- At its ends a face joins the neighbouring floor by a smaller step: its
  lowest column was 1 px above the floor next to it on both faces
  measured from below, and its highest column 3 px below the floor next
  to it on both faces measured from above. Those neighbouring columns
  are not face columns.
- A **vertical step** is two adjacent columns whose floors differ by
  **64 px**. This is the only step height of this kind that was measured.

The rules below were measured near faces and the vertical step. On flat
floor and on slopes of 1 px per 4 columns they never take effect: there
are no face columns or steps, and the column 5 ahead (below) is never
more than 2 px higher.

## Stopped by a rise

On a grounded frame (walking, running or standing; not a roll), after
the speed for the frame is worked out (`ground-speed.md`, or the face
rule below), the player is **blocked** if the move would take `x` into a
different column that is either:

- a face column of a face that rises in the direction of the move, or
- beyond a vertical step up in the direction of the move.

A blocked frame:

- `x` is set to the left edge of the column the player was in before the
  frame: the fraction of a pixel is dropped and `x` is a whole number.
  The player moves back slightly if moving right and forward slightly if
  moving left.
- `vx` becomes **0**.
- The player stays grounded on the floor they were on.

Even a face column only 1 px above the player's floor blocks: the floor
height of the new column is not what counts, only that it belongs to a
rising face. Columns in front of a face are walked over normally right up
to its first column.

Measured at walking and running speed, and at the low speeds of starting
from rest, into faces rising to the right and to the left and into the
vertical step going left; the speed and the fraction of `x` before the
frame made no difference.

## Can't start toward a rise

On a grounded frame that starts with `vx` exactly **0**, with Left or
Right held (with or without Action held, but not on a frame on which an
Action press starts a roll, `roll-input.md`):

1. Take the column **5 columns ahead** of the player's column in the held
   direction.
2. If that column's floor is **9 px or more** higher than the floor of
   the player's column, the player doesn't start moving: `vx` stays 0
   and `x` doesn't change.
3. If it is 5 px higher or less (or lower), the player starts moving as
   usual (`ground-speed.md`). Differences of 6 to 8 px weren't measured.

This holds on every frame it applies, however long the direction is held
and however often it is released and pressed again. It is why a player
who has been blocked stays where they are while still holding toward the
rise: after the blocked frame `vx` is 0 and the column 5 ahead is inside
the face or past the step. Holding the other direction starts moving away
as usual.

The same rule decides whether a player standing a few pixels in front of
a rise can walk up to it at all. A player who does start moving is only
stopped by the blocking rule above, so they can end up closer to the rise
than they could have started from.

Measured standing at every 1/16 px across the boundary in front of a
face rising to the right, a face rising to the left and a vertical step
(rising to the left), and at the positions where players came to rest
by themselves.

## Speed on a face

A frame **starts on a face** when the player is grounded and their
column before the frame is a face column. On such a frame the target
speed of `ground-speed.md` is replaced by:

| Held (Action not held) | Target, face falling to the right | Target, face falling to the left |
|---|---|---|
| Nothing | +1.5 (+384/256) | −1.5 (−384/256) |
| The uphill direction | +1.5 (+384/256) | −1.5 (−384/256) |
| The downhill direction | +3.5 (+896/256) | −3.5 (−896/256) |

That is 1.5 px per frame downhill, plus the normal walking target when
the downhill direction is held. A held direction that points uphill is
ignored. Then the speed rule of `ground-speed.md` applies unchanged
(gap ÷ 8 rounded toward zero, or the target itself when that is 0) and
`x` changes by the new `vx`.

- The frame on which the player moves **onto** a face from other floor
  doesn't start on a face, so it uses the normal target. The face target
  applies from the next frame.
- The face target also applies on the frame on which the player leaves
  the ground from a face (frame L below), because that frame started on a
  face.
- A player can't stand still on a face: with nothing held, the speed
  moves toward 1.5 px per frame downhill.
- Facing didn't change on any measured frame that started and ended on
  face columns, including up to 10 frames in a row with the uphill
  direction held. Whether a longer hold turns the player on a face isn't
  covered.

## Following a face down, or leaving it

On every grounded frame, after the horizontal move (and if the player
wasn't blocked), compare the floor of the new column with the floor of
the column before the frame:

- **8 px lower or less**: the player stays grounded and `y` becomes the
  new column's floor height. `ground` stays 1 and, as on any grounded
  frame, `vy` in a trace is 0. A new column 1 px higher (as at the end
  of a face, or on a gentle slope) is followed the same way; higher
  floor ahead is either blocked (above) or not covered.
- **11 px lower or more**, or **no floor** in the new column: the player
  leaves the ground. This frame is the leave frame **L** of
  `walk-off.md`: `y` moves down by exactly 3.4375 px whatever the floor
  below, `vy` ends at 0.00390625, and the fall continues as
  `walk-off.md` describes, with horizontal speed by `air-control.md`
  from L + 1 on and the landing rule of `landing.md`.

Drops of 9 and 10 px can't happen on a face whose columns are 4 px
apart and weren't measured. On such a face the rule means: crossing at
most **2 columns** in a frame keeps the player on the face; crossing 3 or
more makes them leave it. At the speeds above that depends on the speed
and on the fraction of `x`, so a player walking onto a face usually
follows it for a few frames and then leaves it.

## Leaving a face onto floor

When a grounded player moves off the low end of a face onto other floor
and stays grounded, then on the **first two frames** that start on a
column that isn't a face column:

- a held direction pointing **back up the face** (opposite to the face's
  downhill direction) is ignored: the target speed is 0, as if no
  direction were held;
- a held direction pointing the other way (the way the face falls)
  applies normally.

From the third such frame the normal target applies. Measured moving
left off the foot of a face falling to the left, with Right held (three
runs: target 0 on two frames, then the normal target) and with Left held
(two runs: normal target from the first frame). Leaving a face moving
right in this way, and facing on these frames, are not covered.

## Examples

Synthetic floors. `y` is down-positive, as in a trace.

**Walking into a rise.** Flat floor at `y` = 0 for columns up to 199. A
face rises to the right from column 200: column 200 at `y` = −1, 201 at
−5, 202 at −9, and so on (4 px per column). Walking right at +2 with
Right held, `x` = 193.5 before frame 1:

| Frame | `x` | `vx` (1/256) | Note |
|---|---|---|---|
| 1 | 195.5 | 512 | |
| 2 | 197.5 | 512 | |
| 3 | 199.5 | 512 | |
| 4 | 199 | 0 | Blocked: 201.5 is in face column 201 |
| 5 on | 199 | 0 | Right still held: column 204 is 17 px higher |

**Standing in front of the same rise**, Right held from frame 1:

- At `x` = 196.875 (column 196): column 201 is 5 px higher, so the
  player starts walking, as on flat floor: `x` is 197.125, 197.59375,
  198.25390625 and 199.078125 on frames 1 to 4 (`vx` 64, 120, 169, 211 in
  1/256). On frame 5 the move would reach 200.046875 (column 200), so the
  player is blocked: `x` 199, `vx` 0. They now stand closer to the rise
  than they could have started from.
- At `x` = 197 (column 197): column 202 is 9 px higher, so `vx` stays 0
  and `x` stays 197 on every frame.

**Coasting down a face.** Flat floor at `y` = 0 for columns up to 299; a
face falling to the right from column 300 (`y` = 2, then 6, 10, … for
columns 300, 301, 302, …). Nothing held, `x` = 299 and `vx` = 1
(256/256) before frame 1:

| Frame | Column before the frame | `vx` (1/256) | `x` | `y` | Note |
|---|---|---|---|---|---|
| 1 | 299 | 224 | 299.875 | 0 | Normal target 0 |
| 2 | 299 | 196 | 300.640625 | 2 | Onto the face: normal target 0 |
| 3 | 300 | 219 | 301.49609375 | 6 | Face target +384 |
| 4 | 301 | 239 | 302.4296875 | 10 | |
| 9 | 306 | 308 | 307.98046875 | 30 | |
| 10 | 307 | 317 | 309.21875 | 38 | Crossed 2 columns: 8 px drop, followed |
| 11 | 309 | 325 | 310.48828125 | 42 | |

`vx` keeps rising toward 384 and the player follows the face, crossing
one or two columns per frame.

**Walking down the same face.** Right held, `x` = 297.75 and `vx` = 512
before frame 1:

| Frame | `vx` (1/256) | `x` | `y` | `ground` | Note |
|---|---|---|---|---|---|
| 1 | 512 | 299.75 | 0 | 1 | |
| 2 | 512 | 301.75 | 6 | 1 | Onto the face: normal target |
| 3 | 560 | 303.9375 | 14 | 1 | Face target +896; 8 px drop, followed |
| 4 | 602 | 306.2890625 | 17.4375 | 0 | 3 columns: leaves (L), `y` + 3.4375 |
| 5 | 591 | 308.59765625 | 17.87890625 | 0 | Air: normal target +512 |

From frame 5 on, `vy` and the landing follow `walk-off.md` and
`landing.md`.

## Not covered

- Rolls on, onto or into faces and walls, and Action held on a face
  (with or without a direction).
- Jumping from a face, and jumps from a standstill next to a rise (on
  the press frame the player stays put as above, but the take-off frame
  was seen to move).
- The frames after landing on a face from the air. Contact with faces
  and walls while airborne is in `air-walls.md`, except falls into pits
  that carry the player below the floor of the column they are in.
- Moving left off the low end of a face that ends at a drop rather than
  at floor: the player was seen to leave the ground while still over the
  face's last column. (Moving right off such an end, the rule above
  holds.)
- Leaving a face onto floor while moving right, and facing on the frames
  after leaving a face.
- Vertical steps of other heights, steps up of 9 to 63 px in general,
  faces steeper or less steep than 4 px per column, and a vertical step
  rising to the right.
- Differences of 6 to 8 px in the "can't start" rule.
- How a trace's `state` names frames on a face (the vocabulary has no
  name for being carried down a face).
- Entities, moving platforms and the other character.
