# Riding on steep faces

Titles: `title-1`

How the player riding the animal (`riding.md`) moves on and near steep
faces (`steep-faces.md`): being pushed down a face, held directions that
point uphill, jumps and turns started on a face, being blocked by a
rising face, leaving the floor at a face (including at its low end over
a drop), walls met in the air, and when a jump's landing on a face takes
effect. Riding on flat floor is in `riding.md` and on 1-in-4 slopes in
`riding-slopes.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are as in a trace: right is positive, `vx` in px per frame. Vertical
positions are **level y** as in `view-vertical.md`: they grow downward,
and `vy` is positive when moving down. "Higher" means a smaller `y`.
Speeds are whole multiples of 1/256 px per frame. The parts of riding
(riding, turn, a jump's crouch, take-off, air and landing frames,
charge, the hop, charge end, settling frame), the target speed, the
speed rule with its N of 8 or 32, the frames P, T, L, C and "steered"
are as in `riding.md`. Floor heights and "the column containing `x`"
are as in `slopes.md` ("Floor height"). Steep faces, face columns, and
faces that rise or fall are as in `steep-faces.md` ("Columns, faces and
steps"): each face column's floor is exactly 4 px from its neighbour's.

Measured with the first character alone, by inputs only, unmodified, on
the steep faces near the first level's own crate: a face falling to the
right whose low end meets a drop (no floor), and beyond the drop a face
rising to the right, with flat floor and 1-in-4 slopes before them.
Random inputs as in `riding.md`:

- 240 runs, 84 303 frames, used to find the rules;
- 340 further runs, 79 192 frames, recorded afterwards with new seeds to
  test them.

In 580 runs and 163 495 frames, `x`, `y`, `vx`, `vy`, `ground`, `facing`
and the part of riding were exactly as the rules below give, except in
3 frames, all of one kind listed under "Not covered" (a fall from over
the drop onto the face's low end). 6 616 frames ended with `x` in a face
column, 3 020 of them on the floor. The runs held 349 departures from the floor
other than jumps and hops (180 of them at a face's low end), 51 blocked
frames on the floor, 34 walls met in the air, 1 628 frames to which the
push below was added, and 26 jump landings on or before A + 5 (last
section). Checking stopped once a rider had dropped below the top of the drop,
from where it falls out of the level (`falling-out.md`). The rules also
leave every earlier riding check unchanged (`riding.md`,
`riding-slopes.md`, `riding-walkers.md`).

## Summary

On a face the rider is pushed downhill by 1.5 px per frame added to its
target speed, ignores a held direction that points uphill (for speed
and for turns), can't start a jump, is blocked by rising face columns,
and leaves the floor when the floor drops away by more than 8 px, or
whenever it starts a frame on the low end of a face that ends at a drop.
In the air it meets walls as a walking player does. On flat floor and
1-in-4 slopes none of these rules takes effect, so riding there is as in
`riding.md` and `riding-slopes.md`. One more rule, about when a jump's
landing takes effect, applies to every riding jump but only made a
difference on faces.

## Frames that start on a face

A frame **starts on a face** when, at the end of the frame before, the
rider was on the floor and the column containing `x` was a face column.
For this rule, a jump's take-off frame T and a hop's take-off frame
count as ending on the floor (as they do in `riding.md`), so the first
airborne frame after a take-off from a face still starts on a face.

## Directions that point uphill

On a frame that starts on a face, a held Left or Right that points
**uphill** on that face (Left on a face falling to the right, Right on a
face falling to the left) is ignored:

- for the target speed, which is 0 as if no direction were held (with
  or without Action);
- for turns: it doesn't start a turn. A held direction that points
  downhill starts a turn as usual (`riding.md`).

On steered frames facing still follows the held direction, uphill or
not (`riding.md`, "Steered frames").

## The push

The rider carries a **push**, which is 0, +1.5 (+384/256) or
−1.5 (−384/256) px per frame. It is worked out at the end of every frame
and used on the next one:

1. If the rider ended the frame in the air, or the frame was steered,
   the push becomes 0. (A take-off frame counts as ending on the floor.)
2. Otherwise, if the column containing `x` at the end of the frame is
   not a face column, the push becomes 0.
3. Otherwise, on a face falling to the right: if this frame's target
   speed (after the rule above, before any push) is 0 or more **and**
   the new `vx` is 0 or more, the push becomes +1.5. If either points
   left, the push keeps the value it had. On a face falling to the left
   the same holds mirrored: target 0 or less and `vx` 0 or less give
   −1.5, otherwise the push is kept.

On a frame that isn't steered, the push from the end of the frame
before is added to the target speed, and the speed rule of `riding.md`
(N = 32) applies to the sum. On a steered frame it isn't added.

So a rider on a face with nothing held, or holding the uphill direction,
speeds up toward 1.5 px per frame downhill, a 32nd of the gap at a time.
Holding the downhill direction gives a target of 3.5, or 5 with Action
(512 + 384 or 896 + 384, in 1/256). The push starts one frame after the
first frame that ends on the face, and later still after steered frames
(examples below).

## Jumps

A jump doesn't start on a frame that starts on a face, including the
frame after a hop's take-off from a face (`riding.md` lets a jump start
there on other floor). A press that couldn't start a jump is still
remembered as in `riding.md` and starts one on the first frame that
allows it, within the same 11 frames. (220 frames in the runs held a
usable press that didn't start a jump because of this rule.)

## Blocked by a rise

On a frame that starts with the rider on the floor, after the speed
rule, the rider is
**blocked** if the move would take `x` into a different column that is a
face column of a face rising in the direction of the move. As in
`steep-faces.md`: `x` is set to the left edge of the column it was in
before the frame (a whole number), `vx` becomes 0, and the rider stays
on the floor it was on unless the next section makes it leave.

## Leaving the floor

On a frame that starts with the rider on the floor (riding, a turn, a
jump's crouch, landing frames, a charge on the floor, a charge end, the
settling frame), after the horizontal move, the rider **leaves the
floor** when:

- the column containing the new `x` has no floor, or its floor is
  **12 px or more** lower than `y` (floor 8 px lower or less is followed,
  as in `riding-slopes.md`; 9 to 11 px weren't seen, since face columns
  are 4 px apart); or
- the frame **started on a face column whose downhill neighbour has no
  floor** (the low end of a face that ends at a drop). This happens
  whatever the speed or direction, even when the rider stays in that
  column or is blocked on that frame. So a rider never spends two frames
  in a row on the floor of such a column: the frame after the one that
  brings it there (or lands it there) leaves the floor. Seen at both
  such ends measured (a face falling right and one falling left).

The floor and its height are compared with `y` before the frame. On the
frame the rider leaves the floor, `y` moves down by **3 px plus
the frame's gravity** and `vy` ends at 1/256 (0.00390625); `ground` is 0.
The gravity is that of `riding.md`: 0.28125 while Jump has been held on
every frame since the jump's press frame P (a jump's crouch, or a charge
started from it), so the drop is 3.28125 px; otherwise 0.4375, a drop
of 3.4375 px. This is the leave frame of `walk-off.md`, with the riding
gravity.

From the next frame the rider falls: `vy` increases by the gravity each
frame, never above 8, as in `riding.md`. The speed rule of `riding.md`
applies unchanged (it is not `air-control.md`). The part of riding goes
on, with its frames counted as usual: a fall that starts while riding,
in a turn, on landing frames or in a charge end lands (`landing.md`)
without new landing frames; a fall during a jump's crouch continues as
a jump that starts in the air (`riding.md`); a charge has no hop if it
is in the air on C + 10. Of the 349 falls, 106 landed again (beyond the
drop); the others fell out of the level.

## Walls in the air

On frames that start with the rider in the air (after a jump, a hop or
leaving the floor), walls are met exactly as in `air-walls.md`
("Blocked", "Stepped onto"): a face column higher than the feet blocks,
other floor up to 8 px higher is stepped onto. Blocking sets `x` to the
left edge of the column it was in before the frame and `vx` to 0;
vertical motion goes on. Only face columns and floor a few px higher
were met; the tall sides of the drop are under "Not covered".

## When a jump's landing takes effect

This applies to the landing of every riding jump (not of a charge). On
flat floor and slopes it never made a difference in the runs of
`riding.md` and `riding-slopes.md`; on faces it often did.

During a jump, take:

- **F**, the first frame after the take-off that ends with the rider
  moving down (`vy` > 0);
- **A** (the **approach frame**), the first frame after F at whose end
  there is a floor below the rider within reach. Using whole pixels,
  with the feet line `G` = `y` rounded up and the floor's `y` written
  `Y`: the highest floor of the column containing `x` with `Y` ≥ `G`,
  and both `Y` − `G` ≤ **71** and its row at most **2** rows below the
  feet's row, where the row of a whole-pixel level y `v` is
  (`v` − 1) ÷ 32 rounded down (rows are `y` 1 to 32, 33 to 64, and so
  on). The landing frame L itself can be A (the feet are on the floor
  there).

Let L be the landing frame. Then:

- If L ≥ A + 6: the landing is as in `riding.md`: steering goes off at
  the end of L and riding resumes on L + 8.
- If L ≤ A + 5: steering stays on up to and including frame A + 5, and
  riding resumes on **A + 14** (the landing frames are L to A + 13). On
  L = A + 5 that is only one frame later than `riding.md`. When L is
  earlier, frames L + 1 to A + 5 are steered landing frames: speed step
  ÷ 8, facing follows the held direction, no push, and no jump starts
  on them (a remembered press can start one later). An Action press on
  them starts a charge, as on any landing frame.

In the runs, most early landings came from falls that crossed over the
drop before reaching the face, so A came late. In the first 240 runs,
A was also checked directly against the rider's pose, which changes on
A: 525 of 527 jumps gave the frame above; the two others fell into the
drop, or landed while still moving up.

## Examples

Measured, with positions made relative. The floor is synthetic: `y` = 33
for columns up to 99, a face falling to the right at columns 100 to 107
(`y` = 36, 40, 44, …, 64; 100 is its top end, 3 px below the floor
before it), and no floor from column 108 on. "Column floor" is the
floor of the column containing `x` at the end of the frame.

**Landing on a face, holding the uphill direction.** Left held
throughout, Jump not held. The jump's apex is behind; the rider is
falling to the left over the drop:

| Frame | Part | `x` | Column floor | `y` | `vx` (1/256) | `vy` (1/256) | `ground` |
|---|---|---|---|---|---|---|---|
| 4 | air | 109.76953125 | none | 4.5625 | −202 | 1120 | 0 |
| 5 | air | 108.83203125 | none | 9.375 | −240 | 1232 | 0 |
| 6 = A | air | 107.76171875 | 64 | 14.625 | −274 | 1344 | 0 |
| 7 | air | 106.578125 | 60 | 20.3125 | −303 | 1456 | 0 |
| 9 | air | 103.921875 | 48 | 33 | −351 | 1680 | 0 |
| 10 = L | landing | 102.47265625 | 44 | 44 | −371 | 0 | 1 |
| 11 | landing, steered, blocked | 102 | 44 | 44 | 0 | 0 | 1 |
| 12 | landing | 102 | 44 | 44 | 0 | 0 | 1 |
| 13 | landing | 102.046875 | 44 | 44 | 12 | 0 | 1 |
| 14 | landing | 102.13671875 | 44 | 44 | 23 | 0 | 1 |
| 18 | landing | 102.90234375 | 44 | 44 | 64 | 0 | 1 |
| 19 | landing | 103.19140625 | 48 | 48 | 74 | 0 | 1 |
| 20 | riding | 103.515625 | 48 | 48 | 83 | 0 | 1 |

- Frame 5 ends over column 108, which has no floor, so it isn't A.
  Frame 6 ends over column 107: feet line 15, floor 64, 49 px below; row
  of 15 is 0, row of 64 is 1. So A = 6.
- Frame 10 lands: it would end at 33 + 7 = 40, 4 px above the floor of
  column 102 (`landing.md`). L = 10 is less than A + 5 = 11.
- Frame 11 starts on a face, so Left is ignored (target 0). It is still
  steered: step (0 + 371) ÷ 8 = 46, `vx` −325, which would take `x` into
  column 101, a face column rising to the left: blocked, `x` = 102,
  `vx` 0. It is steered, so the push becomes 0.
- Frame 12: target 0, no push: `vx` stays 0. At its end the target (0)
  and `vx` (0) are both 0 or more, so the push becomes +1.5.
- Frame 13: target 0 + 384, step 384 ÷ 32 = 12. The rider creeps down
  the face while holding Left.
- Riding resumes on A + 14 = 20, not L + 8 = 18. Had A been 5 or
  earlier, frame 11 would not have been steered, the push would have
  been set at its end, and `vx` would have been 12 on frame 12.

**Leaving at the low end.** Left held throughout; on the floor at rest
at `x` = 106 before frame 1, with the push already +1.5 (set at the end
of frame 0):

| Frame | `x` | Column floor | `y` | `vx` (1/256) | `vy` (1/256) | `ground` |
|---|---|---|---|---|---|---|
| 1 | 106.046875 | 60 | 60 | 12 | 0 | 1 |
| 2 | 106.13671875 | 60 | 60 | 23 | 0 | 1 |
| 6 | 106.90234375 | 60 | 60 | 64 | 0 | 1 |
| 7 | 107.19140625 | 64 | 64 | 74 | 0 | 1 |
| 8 | 107.515625 | 64 | 67.4375 | 83 | 1 | 0 |
| 9 | 107.76953125 | 64 | 67.87890625 | 65 | 113 | 0 |
| 10 | 107.953125 | 64 | 68.7578125 | 47 | 225 | 0 |
| 11 | 108.0703125 | none | 70.07421875 | 30 | 337 | 0 |

- Frames 1 to 8 start on a face: target 0 (Left ignored) plus the push.
- Frame 7 moves onto column 107 and follows it down to 64.
- Frame 8 starts on column 107, whose right neighbour has no floor, so
  the rider leaves the floor although it stays in column 107: `y` =
  64 + 3 + 0.4375, `vy` 1/256.
- Frame 9 starts in the air, so Left counts again (target −512) and
  there is no push: step (−512 − 83) ÷ 32 = −18, `vx` 65. `vy` =
  1 + 112. The rider then drops past column 107 into the drop.

## Not covered

- Landing on a face's low end from the air over the drop next to it:
  in the 3 frames that differed from the rules, a rider falling from
  over the drop into the low-end column didn't land there, although the
  landing rule says it would. Falls that carry the rider below the top
  of the drop are not covered either (`air-walls.md`).
- "Can't start toward a rise" (`steep-faces.md`) never came into play
  (there was no rise to start toward), so whether it applies to riding
  isn't known.
- Leaving a face onto floor at its low end, and faces whose low end is
  floor rather than a drop. Here both faces' low ends meet a drop.
- Floors 9 to 11 px lower in the leaving rule, steps and walls other
  than 4-px faces, and faces of other steepness.
- Whether the first airborne frame after a take-off is blocked as on the
  floor or meets walls as in the air: both rules gave the same result in
  every case seen.
- A jump that lands before F (while still moving up, after meeting a
  wall in the air).
- Whether the approach-frame rule, the take-off rule for the push and
  the low-end rule also apply to the walking player.
- Trace state names for riding (as in `riding.md`).
