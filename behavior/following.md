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
the lead is hurt or falls out of the level, or the follower repeats a
bounce of the lead ("Walkers and the follower").

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

## The follower's pictures

While it follows the trail, the follower is drawn with pictures of the
second character (`player-frames.md`: the `s-` pictures), never the
lead's. This section gives only the walking and running pictures; the
others are under "Not covered".

- **Which mode.** Whether the follower is walking or running is not
  specified by the trail, which holds positions only. As a fallback an
  engine can use the lead's mode (walking or running) on the frame it
  was at the follower's current point: in the sample below the follower
  showed that mode on 1269 of 1486 frames on which it moved and the
  lead's mode at that point was walking or running (85%). Most of the
  rest were frames on which the follower stood in its standing picture
  while moving under 1 px per frame.
- **Cycle start.** When the follower enters walking from any other mode,
  its first picture is `s-walk-1`; when it enters running (from walking
  too), it is `s-run-1`. The cycle never carries over from an earlier
  walk or run. Seen on every one of 186 entries into walking and 42 into
  running whose first picture could be read.
- **Timing.** Pictures follow in order, round the 16 walking pictures or
  the 13 running ones, and each usually lasts 2 logic frames. Of 1916
  picture changes in walking that could be read at both ends, 1860 came
  2 frames after the one before (97%), 9 after 1, 39 after 3 and 8
  after 4; of 313 in running, all 313 came after 2. The other gaps are
  not explained. The follower's speed does not change this: the 2-frame
  steps held at up to 5 px per frame in running (313 of 313 changes), so the speed-dependent stride rule of the second character as the
  player (`ground-speed.md`) does not apply to the follower, whose speed
  is that of the trail.

Evidence: 24 runs of 2500 frames with the first character leading and
random play, rows where the lead was not changed by a knock-out in the
8 frames before and the follower was not hurt.

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

## Walkers and the follower

A walker (`walker.md`) has no effect on the follower. It neither hurts it
nor gives it a bounce, whatever the follower is doing and wherever the
walker is, and it walks on as if the follower weren't there. Only the
lead is hurt or bounces (`walker-contact.md`, `knock-out.md`).

When the lead bounces off a walker, the follower goes through the same
stretch of the trail later, so it rises and falls where the lead did,
in the pictures of a bounce, although no walker may be near it.

Measured on the first level, with the second character leading and the
first following. A setup method arranged the situations; what was seen
on screen is described here. A walker heading left at full speed was
seen at every whole-pixel offset from the follower, from 45 px to its
left to 30 px to its right (45 px for the small walker), for 3 or 4
frames at each: with the follower standing at rest, with the walker level
with its feet and up to 10 px below them; and with the follower in the
air on the spot where the lead had jumped while walking away (the lead
about 50 px away), on each frame from the 51st to the 68th after the
lead took off, with the small walker on the floor under it and up to
44 px below its feet, and with the medium and the large walker
(`walker-contact.md`, "Kinds of walker") on the floor under it on three
of those frames. The follower's position and way of moving were the same
as without the walker in every case. The lead, with a walker at the same
offsets from it, was hurt at the offsets `walker-contact.md` gives; with
the walker near the follower only, it was not. A small walker that
walked through the follower at rest on its own didn't change it either,
and then hurt the lead on reaching it. In the 161 runs by inputs of this
file's first paragraph the follower was never hurt, and in each of the 23
runs where it showed a bounce, the lead had shown it earlier.

## Joining

An object that holds the missing character (`carrying.md`) frees it
when it breaks: a thrown one breaks when it first touches the ground, a
wall, a barrier or an enemy, or leaves the picture
(`holder-breaking.md`, which also says where it is then). Call **S** the
frame on which it breaks. From S + 1 to S + 33 walkers and the thrower
don't move either (`holder-breaking.md`).

- If both characters are already there, nobody comes out, and nothing
  waits from S + 1 on (`holder-breaking.md`, "With both characters
  there").
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

### The lead during the wait

When a character joins, the lead doesn't move from S + 1 to S + 33, on
the ground or in the air: its position, its speeds, its facing and what
it is doing (standing, walking, a jump at any point of its arc, a roll,
the stop after a roll, a ground action, the end of a throw) stay as they were on S.
If the lead is in the air on S, (`X1`, `F1`) is that spot in the air and
the flight above ends there, also in the air.

On S + 34 the lead goes on exactly as it would have gone on from S + 1
if nothing had broken, given the same input from then on, with two
exceptions, both about presses:

- **Input from S + 1 to S + 33 does nothing**, but S + 33 is still the
  frame before S + 34: a press of Jump or Action is a frame where the
  button is held after a frame where it isn't (`roll-input.md`). So Jump or Action pressed during
  the wait and still held on S + 34 is not a press on S + 34 or later:
  no jump, roll or ground action starts from it, on the ground or on the
  landing (`jump-input.md`, "Pressing before landing"), even if it was
  pressed on S + 33. A new press is needed. A button pressed and let go
  during the wait does nothing at all. A button not held on S + 33 and
  held on S + 34 is a press on S + 34, whatever was held on S.
- **A press made on S or earlier is no longer remembered on S + 34**: a
  Jump press in the air waiting for the landing (`jump-input.md`,
  "Pressing before landing") and an Action press that `roll-input.md`
  remembers for a later roll start nothing after the wait.

Held buttons work as on any other frame from S + 34: the directions,
Down, Up, Jump held during a jump and Action held for running are read
again on S + 34.

Enemies wait too (`holder-breaking.md`, "Everything else waits"), so an
enemy the lead meets after S + 34 is where the wait left it, not where it
would have been without the wait.

Measured:

- **The flight.** Exact on every frame of 5 744 joins: the two joins
  measured first (one object thrown onto the floor 117 px from the lead,
  with the second character joining, and one thrown against a wall next
  to the lead, with the first character joining again after it was lost,
  start 7 px above the lead's feet line); 44 throws by inputs that broke
  on the floor (40), against the barrier (2) or at the edge of the
  picture (2); 23 throws at a walker by inputs, 16 of them breaking on
  it (4 of those in the hands during the throw); 1 throw that broke on a
  ball; 120 objects set next to the thrower, moving left or right, that
  broke on it, starting 30 to 91 px to the side of the lead and 132 to
  182 px above it; and the 5 554 runs below.
- **The lead.** 5 554 runs on flat floor with the second character
  joining, random input before, during and after the wait, and the
  object held where it was in its flight for up to 70 frames (by
  setting its position) so that S fell anywhere in the lead's jumps: on
  S the lead was on the ground in 2 802, on the frame a jump started in 122, rising in
  1 937 and falling in 693. Each was compared over the 70 frames from
  S + 34 with the same run with no break (the object taken away on
  S − 1) and the same input from S + 1. The lead didn't move from S + 1
  to S + 33 in all of them, and all 2 381 runs of the second set of
  random runs whose input from before S was kept were explained: 2 069
  went on exactly the same, 309 differed through a press on S + 34 or
  on S + 1 of the run with no break (the first rule), and 3 through a
  press remembered from S or before. In the first 3 173 runs, 2 737 went
  on the same, 427 differed with Jump or Action held on both S + 33 and
  S + 34, and of the other 9, replayed, 3 were presses on S + 34, 5 were
  remembered presses, and in 1 the lead's ground action knocked out a
  walker in the run with no break only (`ground-action.md`).
- **Presses, one by one.** Jump and Action each pressed on S + 1,
  S + 10, S + 20, S + 30, S + 32 or S + 33 and held, pressed and let go
  within the wait, or pressed on S + 34, with the lead standing on S and
  with it in the air on S and landing 1, 5 or 9 frames after S + 34;
  and Jump or
  Action pressed on S or 1 to 13 frames before it and held through the
  wait, with the lead falling: each case as the rules above give, next
  to the run with no break with the press 33 frames earlier.

## When a level begins again

The characters that are there, and which one leads, carry over:

| What happened | Measured | When play resumes |
|---|---|---|
| The level is finished with both characters | first leading; second leading | both, the same one leading |
| Both fall out of the level (`falling-out.md`) | first leading | both, the first leading |
| The only character left is knocked out (`knock-out.md`) | the second character, after the first was lost | the second character only |
| The only character left is knocked out | the first character, after the second was lost (a swap first) | the first character only |

When both are there, both appear where play starts, and the one that
leads is under control. After a knock-out the other character is missing
again, and a holder frees it again (`restart-barrel.md`, "Holders, and
who leads").

Both falling out with the second character leading was also measured
after the restart point was reached: both were there when play resumed,
the second leading.

### At the restart point

When play resumes at the restart point (`restart-barrel.md`,
"Resuming", whose frame numbers are used here) with both characters
there, the lead moves as given there, whichever character leads, and
the follower:

- **On frame 0** is at the restart point, at the same position as the
  lead. The trail holds 16 points, all at the lead's whole-pixel
  position on frame 0, and the follower's current point is the first of
  them. The catching-up mark is clear.
- **On frame 1** follows the trail as on any frame ("Following the
  trail"), and the lead's point for frame 1 is added as usual.
- **On frames 2 to 62** doesn't move, and nothing is added to the trail
  (the lead doesn't move on these frames either).
- **From frame 63 on** follows the trail and rests by the rules above.

While the follower is at the restart point, it turns as if it stood on
the ground there ("Facing": a 7-frame turn, not an immediate one). Like
the lead, it isn't drawn while the barrel stands; both characters were
first drawn on the same frame after the barrel burst.

The restart point isn't on the ground (in the first level it is 9 px
above the floor), so from frame 63 the follower steps on one point per
frame through the copies of that point: it stays at the restart point
until frame 77 and is at the lead's point from frame 1 on frame 78.
From there it goes along the lead's path. With no input, for example,
the lead falls from frame 63 and lands on frame 70. The follower then
falls along the same heights from frame 78, lands on frame 85 and rests
from there: it walks to the spot 28 px behind the lead.

Measured on the first level with both characters falling out together
after the restart point was reached, first or second leading, and then
playing on from frame 0 of the resume: 66 runs of up to 343 frames, each
of 33 input sets with each lead. 21 sets were chosen: no input; Right,
Left, Jump or Down + Action held on frame 1 (Jump also held again from
frame 63); walking, running or jumping from frame 63, 76, 81 or 90;
short walks and stops; crouching. 12 were random stretches of standing,
walking, running, jumps and crouching from frame 63. On 16 666 of the
17 079 frames the follower's whole-pixel position and its way of moving
(trail or resting) matched these rules. The other 413 frames were in 3
runs. In each, the first difference was 1 px in x while the follower
rested on sloped ground (see "Not covered"), and the other differences
came from it. 21 runs stopped when the lead was hurt by a walker and
one when the follower repeated the lead's bounce off one. The facing
differed on 103 frames: 62 of them within 10 frames of one of the
follower's take-offs or landings, as in "Facing", and 41 on turns, while
it rested or walked, that came on other frames than "Facing" gives (see "Not covered"). In 30 more runs
(falling out from 5 places, with each lead, no input, Right from frame 1
or Right from frame 63), both characters were at the restart point on
frame 0 of the resume.

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

- Enemies other than walkers touching the follower, and thrown objects
  breaking on it.
- The follower after it has repeated a bounce of the lead: its position
  and way of moving were checked only up to that frame (see the opening
  paragraph and "Walkers and the follower"). In runs that went on past
  it, most showed differences of a few pixels later, which weren't
  looked into, so the rules above aren't claimed for those frames.
- The lead carrying an object, swapping, being hurt, bouncing off an
  enemy (beyond the pause frames), or in any action other than those in
  the runs above (standing, walking, running, rolling, crouching,
  jumping, falling), and the follower during a swap (`swap.md`) or a
  take-over (`losing-a-character.md`).
- The follower's pictures while it rests (the two standing pictures
  alternate on a period not found), while it turns, on the airborne
  frames of the trail (the jump pictures were not checked frame by
  frame), and after a bounce; and the exact frames of its turns around
  its take-offs and landings on the trail.
- Resting on ground that isn't flat or gently sloped, and a rest spot
  across a ledge.
- Some of the follower's turns while it rests or walks after a resume
  at the restart point came on other frames than "Facing" gives (41
  frames in the runs of "At the restart point").
- Where each character is when play begins again at the level's start
  with both there (at the restart point: "At the restart point"). How
  the characters enter the first level when it begins is in
  `level-start.md`.
- Joining: where the joined character goes from S + 34 when the lead
  was in the air on S (it then stands on the trail at a spot in the air),
  joins of the first character other than the one above, joins in other
  levels, ground other than flat floor, Swap pressed during the wait or
  on S + 34, and the lead in an action other than those listed in "The
  lead during the wait". Presses made more than 13 frames before S
  weren't tried.
- The second character being knocked out with both there, and a level
  finished or fallen out of with only one character.
