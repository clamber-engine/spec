# The other character: following and joining

Titles: `title-1`

How the character that isn't under control moves while the two
characters are together: where it tries to be, how it gets there on each
frame, how it faces, and how the missing character joins. Also which
characters are there, and which one leads, when a level begins again.

Frames are logic frames at 60 Hz, numbered as in a trace. Positions are
**level x** and **level y** (`formats/level-ground.md`,
`view-vertical.md`): right and down are positive, in px, whole multiples
of 1/256 px. A character's **feet line** is its level y rounded up to a
whole px, as in `view-vertical.md`, and its **whole-pixel position** is
(⌊x⌋, feet line). "The ground below" a point is as in
`formats/level-ground.md` ("The ground below a point"); a character
**stands on the ground** at a point when the ground below that point's x
and feet line is that feet line.

The **lead** is the character under control; the **follower** is the
other one. The **first character** is the one under control when a game
begins; the **second character** is the one that joins later.

Measured on the first level by inputs alone: 161 runs of up to 400
frames from four starting places (two on the flat ground and slopes
near the start, one of them with the second character leading; a
stretch with walkers; the ground before a pit), made of random stretches of standing, walking, running,
rolling, crouching, jumps held 1 to 20 frames with and without a
direction, and reversals. On every one of the 49 195 frames checked,
the follower's whole-pixel position and its way of moving (trail or
resting, below) match these rules. Runs were checked up to the frame
the lead is hurt or falls out of the level, or the follower itself
lands on a walker (see "Not covered").

## The order within a frame

Each frame, the follower is moved once, using:

- the lead's position, speed, target speed (`ground-speed.md`), facing
  and whether it stands on the ground: as they are at the end of this
  frame when the second character follows, and as they were at the end
  of the frame before when the **first** character follows;
- the trail (next section) as it was at the end of the frame before.

The lead's point for this frame is added to the trail after the
follower has moved.

## The trail

The follower's movement is described with a **trail**: a list of the
lead's whole-pixel positions, oldest first, one of which is the
follower's **current point**. The trail holds the current point and the
points after it, at most 31 points in all.

At the end of each frame, if the lead's whole-pixel position differs
from the newest point, it is added as the new newest point. If the
trail then holds 32 points, its oldest point (the current point) is
dropped and the next point becomes the current point. A lead that
doesn't move, or moves less than a whole px, adds nothing.

So while the lead moves, the follower is never more than 31 points
behind it: a lead in the air or running pulls the follower along, since
every frame adds a point.

## Following the trail

On each frame of following the trail:

1. **Move to the current point.** The follower's whole-pixel position
   becomes the current point's. The parts after the point stay as they
   were (x − ⌊x⌋, and the feet line minus y).
2. **Step on or wait.** Call the **gap** the distance in x between the
   newest point and the follower's new ⌊x⌋.
   - If the follower doesn't stand on the ground at its point (the lead
     was in the air there, or the point is off the ground for another
     reason), the next point becomes the current point.
   - Otherwise a **catching-up** mark decides:
     - gap 28 or more, mark not set: set the mark; no step this frame;
     - gap 28 or more, mark set: the next point becomes the current
       point;
     - gap under 28: clear the mark; no step.

   A step never goes past the newest point: when the current point is
   already the newest, nothing changes.

   The follower is placed on the new current point on the **next**
   frame (step 1), not on this one.
3. **Decide to rest** (next section).

The follower doesn't move on its own while on the trail: its position
is only ever a point the lead was at, so it walks, jumps, falls and
lands where the lead did, at walls, ledges, gaps and slopes, with no
rules of its own. While the lead walks, the follower stays about 28 px
behind it along the path; at a stop it comes up to under 28 px of the
lead's last point and waits there, unless it starts to rest.

## Resting

On a frame of following the trail (after step 2), the follower starts
to **rest** if all of these hold:

- it stands on the ground at its current point;
- the lead stands on the ground, its target speed is 0, and its speed
  is under 0.125 (32/256) px per frame either way;
- the distance in x between the lead's ⌊x⌋ and the follower's ⌊x⌋ is
  not 27, 28 or 29 (that is, it differs from 28 by 2 or more);
- one of the two **rest spots** is reachable. The rest spots are the
  lead's ⌊x⌋ + 28 and ⌊x⌋ − 28. The one **behind** the lead (on the
  side it faces away from: + 28 when it faces left) is tried first. A
  spot is reachable when the ground below that x and the follower's
  feet line exists and is less than 28 px above or below the
  follower's feet line. If neither is reachable, the follower doesn't
  rest and keeps following the trail.

The chosen spot is the follower's **target** `T`.

On each frame of resting, from the frame after it started:

1. Its speed is (`T` − ⌊x⌋) ÷ 16 px per frame, exactly (so 1/16 px per
   frame for each whole px still to go, either way), and it moves by
   that speed. Its feet stay on the ground (on every resting frame
   measured it stood on the ground below it). It stops once ⌊x⌋ = `T`.
2. Then, if the trail has gained a point since the frame resting
   started (including one added at the end of that frame), the follower
   **leaves** rest: the trail is replaced by 17 points (below), and the
   follower stays where it is on the next frame. On the frame after
   that it follows the trail again from step 1.

**The trail after resting.** Call `S` the follower's whole-pixel
position after its move and `P` the newest point before the change. The
new trail is, oldest first: 16 points `S` + (⌊`k` · `dx` / 16⌋,
⌈`k` · `dy` / 16⌉) for `k` = 0 to 15, where (`dx`, `dy`) = `P` − `S`,
then `P` itself. The current point is the first one (`S` itself). So
the follower walks from where it rested toward the lead's last point,
1/16 of the way per point, before it reaches the lead's own path again.

## Facing

The follower turns to face the lead. On a frame of following the trail
on which the newest point's x differs from the follower's ⌊x⌋ (after
step 1), on each resting frame, and on the frame it stays where it is
after leaving rest, it checks whether it faces away from the lead (it faces
left and the lead's ⌊x⌋ is not less than its own, or it faces right and
the lead's ⌊x⌋ is less). If it does, and it isn't already turning:

- if it doesn't stand on the ground at its current point, it faces the
  other way at once;
- otherwise it turns: a turn lasts 7 frames from that frame, and the
  facing changes on the 4th of them.

Measured on the same runs: the facing matched on all but 200 of the
49 195 frames. The differences are around the frames where the follower
takes off or lands while following the trail (see "Not covered").

## Pauses

On the frames when nothing but the count of frames goes on (the two
frozen frames after a bounce, `bounce.md`, and the pause after a roll
defeats a walker, `rolling-into-walkers.md`), the follower doesn't move
and nothing is added to the trail.

## Joining

An object that holds the missing character (`carrying.md`) frees it
when it breaks: a thrown one breaks when it first touches the ground, a
wall, a barrier or an enemy, or leaves the picture
(`holder-breaking.md`, which also says where it is then). Call **S** the
frame on which it breaks. From S + 1 to S + 33 walkers and the thrower
don't move either (`holder-breaking.md`).

- If both characters are already there, nobody comes out.
- Otherwise the missing character joins, whichever it is: also the
  first character, after it ran out of the level when hit
  (`losing-a-character.md`).

The joining character:

- **On S** appears at the object's whole-pixel position on that frame.
  Call it (`X0`, `F0`), and call (`X1`, `F1`) the lead's whole-pixel
  position on S (after the lead's own move on S).
- **On S + `k`, `k` = 1 to 32,** flies to the lead's spot: ⌊x⌋ = `X0` +
  ⌊`k` · (`X1` − `X0`) / 32⌋, and its level y is `L` − `A`, where
  `L` = `F0` + ⌈`k` · (`F1` − `F0`) / 32⌉ is the straight line and `A`
  is its height above the line: `A` = `c` + `U`, with
  `U` = 6 + 6`k` − 0.1875 · `k` · (`k` + 1) px, and `c` the fractional
  part of `A` on the frame before (0 on S + 1). `U` is largest, 51 px, on
  S + 15 and S + 16, and comes back to 0 on S + 32. The x keeps its
  fractional part.
- **On S + 33** stands at (`X1`, `F1`). The trail is replaced by 16
  points from there to the lead's newest point, as in "The trail after
  resting" without the extra 17th point, and the current point is the
  first. From S + 34 it follows the trail (and usually starts to rest
  right away, walking to its rest spot).

The lead doesn't move from S + 1 to S + 33: its position stays as it was
on S, even in the air, and it carries on from there on S + 34.

Measured on two joins: one object thrown onto the floor 117 px from the
lead, with the second character joining, and one thrown against a wall
next to the lead, with the first character joining again after it was
lost (start 7 px above the lead's feet line). Every frame of both
flights matched these rules exactly, and the lead stayed still from
S + 1 to S + 33 in both and in two more throws (walking held, and a
jump in progress).

## When a level begins again

The characters that are there, and which one leads, carry over:

| What happened | Measured | When play resumes |
|---|---|---|
| The level is finished with both characters | first leading; second leading | both, the same one leading |
| Both fall out of the level (`falling-out.md`) | first leading | both, the first leading |
| The only character left is knocked out (`knock-out.md`) | the second character, after the first was lost | the second character only |

When both are there, both appear where play starts, and the one that
leads is under control.

## Example (made-up numbers)

The first character leads on flat ground at feet line 300, and the
follower stands on the ground on its current point.

**Walking.** The lead walks right at 2 px per frame, so each frame adds
a point 2 px after the last. The follower's current point is 15 points
behind the newest (gap 30) and the catching-up mark is set: each frame
it moves to the next point and the gap stays 30, so it walks 2 px per
frame too, 30 px behind the lead's newest point.

**Stopping.** From frame 10 the lead stands still at ⌊x⌋ 1004, facing
right, target speed 0, speed 0. The newest point is (1004, 300), and the
follower's current point is at x 974.

| Frame | Follower `x` | What happens |
|---|---|---|
| 10 | 974 | Moves to its current point; gap 30, so it steps on. The lead is 30 px away (not 27 to 29) and the spot behind it, 976, is on the same ground: it rests, `T` = 976 |
| 11 | 974.125 | Speed (976 − 974) ÷ 16 = 0.125 |
| 18 | 975 | |
| 19 | 975.0625 | Speed (976 − 975) ÷ 16 = 0.0625 |
| 34 | 976 | Speed 0 from here |

**Walking on.** On frame 40 the lead's ⌊x⌋ becomes 1005, and the point
(1005, 300) is added at the end of the frame. On frame 41 the follower,
still resting at 976, leaves rest: `S` = (976, 300), `P` = (1005, 300),
`dx` = 29, and the new trail is x 976, 977, 979, 981, 983, 985, 986,
988, 990, 992, 994, 995, 997, 999, 1001, 1003, then 1005, all at feet
line 300. On frame 42 it stays at 976; on frame 43 it moves to the
first point (976) and follows the trail from there.

## Not covered

- The follower landing on or touching enemies (it can bounce off a
  walker while following, and the rules above then stop holding), and
  enemies touching it.
- The lead carrying an object, swapping, being hurt, bouncing off an
  enemy (beyond the pause frames), or in any action other than those in
  the runs above (standing, walking, running, rolling, crouching,
  jumping, falling), and the follower during a swap (`swap.md`) or a
  take-over (`losing-a-character.md`).
- What the follower is shown doing (walking, jumping, turning poses),
  and the exact frames of its turns around its take-offs and landings
  on the trail.
- Resting on ground that isn't flat or gently sloped, and a rest spot
  across a ledge.
- How the characters enter when a level begins, and where each one is
  when play resumes (`restart-barrel.md` covers where the lead is when
  play resumes at a restart point).
- Joining from an object that breaks on an enemy, joining while the
  lead is in the air, and input on frames S + 1 to S + 33.
- The second character being knocked out with both there, and a level
  finished or fallen out of with only one character.
