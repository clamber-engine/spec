# When an object holding the missing character breaks

Titles: `title-1`

An object that holds the missing character (`carrying.md`) frees it when
it breaks, and the character joins (`following.md`, "Joining"). This file
says on which frame **S** such an object breaks and where it is on S,
from the moment it is picked up until it breaks; everything from S on is
in `following.md`. Since one character is missing, the lead is alone
throughout, except in "With both characters there" below.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`,
`vx` and `vy` are as in a trace: right and down are positive, speeds in
px per frame. `y` is a level y as in `view-vertical.md`. Q is the frame
Action is let go, as in `carrying.md`: the object leaves the player's
hands on Q + 21, and **t** is the number of frames since then (t = 1 on
Q + 22), as in `thrown-objects.md`. Columns, face columns, "higher",
**the speed rule**, I and U are as in `thrown-objects.md`. The object's
**whole-pixel position** is its `x` rounded down and its `y` rounded up,
as in `following.md`; that is where the joining character appears on S.

Measured on the first level with the lead alone (the first character
leading with the second missing, and the second leading after the first
was lost): 81 throws by inputs alone at both places in the level where
such an object lies and at a copy of the object moved in the level's
placement list next to a walker (nothing else changed); 865 frames of
flight checked against the rules below with no mismatch, ending in 59
breaks on the floor, 6 against walls, 2 at a barrier, 2 at the edge of
the picture and 12 on a walker; and about 8 000 controlled frames in
which the object's position or speeds (or a walker's position) were set
just before a frame, there and with a copy of the object moved next to
the thrower, to find the limits.

## A thrown object, each frame

From Q + 22 on, every frame, the object does what a thrown object that
holds nothing does (`thrown-objects.md`, "Each frame" and "In the air"),
except that it **breaks** in each of the cases below instead. The first
case that applies on a frame makes that frame S.

1. **Against a wall.** The wall rule of `thrown-objects.md` ("Breaking
   against a wall"): the move would take `x` into a different column
   that is a face column of a face rising in the direction it moves,
   with its floor ("The floor of a column" there) higher than the
   object's `y` at the start of the frame. Whether the face is passable
   from below makes no difference ("Ground passable from below" below).
   The rule's "A floor more than 8 px higher" breaks it too, with the
   same conditions (a move past face columns into a column that isn't
   one broke it on the same frames as an object holding nothing: 4 at
   solid ground, 140 at a face passable from below).
   On S its `x` doesn't change, but
   its vertical move of this frame ("In the air", steps 1 to 4) still
   happens, in the column of the unchanged `x`, including touching the
   floor there (then its `y` is that floor). The joining character
   appears at that `x` and the new `y`.
2. **At a barrier.** A place that stops walkers (`walker.md`, "Turning
   at a barrier"; the one measured is a vertical step, where an object
   holding nothing breaks too, `thrown-objects.md`, "Breaking at a
   vertical step") stops this object the same way it breaks at a wall: on
   the frame whose move would take `x` below X_b (moving left), it
   breaks, with `x` unchanged and its vertical move done as in case 1.
   Measured at one barrier, moving left: a move ending 0.03 px below X_b
   broke it, one ending 0.22 px above X_b didn't. It broke at every `y`
   measured from 54.5 px above the floor at the barrier to 15.4 px below
   it, and not at 16.4 px below; higher than 54.5 px wasn't measured.
3. **On the floor.** It touches the floor as in "In the air" step 3 (new
   `vy` above 0 and new `y` > `F` − 5) and its `vy` from before this
   frame was **1** (256/256) or more: it breaks instead of bouncing. On S
   its `x` is its moved `x` and its `y` is `F` exactly. A thrown object
   leaves the hands falling at 1 px per frame and only falls faster
   until it first touches something, so **its first touch of the floor
   always breaks it.** (In controlled frames, a touch with `vy` from
   before the frame below 1 made it roll as an object holding nothing
   rolls; that can't happen to a thrown one.)
4. **Outside the picture.** With `V` and `W` the view's x and y as in
   `thrower.md` ("Leaving the picture"), `X` its new `x` rounded down and
   `Y` its new `y` rounded up, it breaks when it is outside the window

       −31 ≤ X − V ≤ 288   and   −31 ≤ Y − W ≤ 256

   at the end of the frame, at its new position. Measured: broke at
   `X` − `V` = −32 and 289 and kept at −31 and 288, broke at `Y` − `W` =
   −32 and 257 and kept at −31 and 256 (the view still). So unlike an
   object holding nothing, a thrown one is never simply removed: leaving
   the picture frees the character too.
5. **On an enemy** ("Enemies" below), with both at their positions at
   the end of the frame. On S it is at its moved position.

If none of these happens, it moves on as an object holding nothing
would. That includes "The floor of a column", the lift, and rising
into ground from below (`thrown-objects.md`, "In the air" and "Ground
passable from below"). It doesn't touch the lead: in 950 controlled
frames with the object placed over and around the lead (up to 48 px to
either side and from 8 px below to 64 px above, at t = 5 and t = 20), it
never broke.
`carrying.md` has no throw upward, so it can't fall back onto the
thrower either.

## Ground passable from below

Ground passable from below (`../formats/level-ground.md`) is met as by an
object holding nothing (`thrown-objects.md`, "Ground passable from
below"): it matters only on a frame on which the object rises into the
ground from below, where solid ground stops it and passable ground
doesn't. A face of passable ground breaks it as other faces do (case 1).

**Measured** with the same controlled starts as there: a thrown object
holding the missing character, at a platform's edge on the first level
whose ground is all passable from below (the edge rises as a face); the
object was a copy moved in the level's placement list to that part of
the level, picked up and thrown there (nothing else changed). 7 614
starts at 6 places from 12 px to 0.5 px before the edge's first column,
at heights from 10 px below the bottom line of the platform's band to
10 px above its top, every 0.25 px, with `vx` 3.5, 8 and 14.4375 px
per frame (896/256, 2048/256, 3696/256) moving right and `vy` −2, 0 and
2 (−512/256, 0, 512/256), 6 frames each, in the real image and in a
copy with every surface made not passable from below.

- Real image and copy: 211 of the rising starts differed, each first on
  a frame on which the object was stopped from below in the copy and
  rose into the platform's band in the real image; every other start
  gave the same frames in both.
- Against the same starts with an object holding nothing: the position
  and speeds were the same on every frame until this object broke. It
  broke on every frame on which that object broke (432 of them against
  the passable face), and also on every frame on which that object
  bounced (1 431 starts: case 3 above). It never broke on any other
  frame.
- Frame by frame, the rules of this file gave every one of the 24 701
  frames that followed a start, in both images. 140 of them were moves
  past face columns into a column that isn't one, on which the object
  broke by "A floor more than 8 px higher" (an object holding nothing
  broke on the same frames); on the frames of objects that had come up
  into the platform from below, it didn't break.
- On every break (3 155 in each image, 1 798 of them on the first frame
  after the start) the joining character appeared at the object's
  whole-pixel position.

## Enemies

### Walkers

A walker is hit exactly as in `thrown-objects.md` ("When a walker is
hit": the ranges of I and the height limits by t), and the object breaks
on that frame, S = K. Measured for a small walker facing left and the
object moving right (77 hits in 105 placements each at t = 5 and
t = 16, 51 in 105 each at t = 17 and t = 20: both ends and the change
from t 16 to t 17 exactly as in the table, and the height limits 63 and
30), plus 12 throws by inputs.

The walker then moves as in "The walker" there, but it **doesn't move
from S + 1 to S + 33** (the join, see "Everything else waits"): on S it
walks and its `vx` becomes −4 × d, its K + 1 step is on **S + 34**, and
its K + 2 on S + 35 (all 12 throws).

### Carried into a walker

A **medium or large** walker meets the object while the player carries
it by the rule for a carried object of `thrown-objects.md` ("A carried
object", "When the walker is hit": the same ranges of I, H at most 55
for a large walker and 42 for a medium one, and no hit on a frame on
which the player bounces off the walker). The object breaks on that
frame: S = K. A small walker on the player's floor never meets it.

- **The joining character** appears on S at the object's whole-pixel
  position, where the object is carried on S.
- **The player** moves on S as if still carrying, doesn't move from
  S + 1 to S + 33 ("Everything else waits"), and goes on from S + 34
  without the object: on the ground it stands or walks, and in the air
  it keeps the airborne carrying pose until it lands.
- **The player isn't hurt by that walker.** With an object that holds
  nothing, the walker can still hurt the player on K + 1
  (`thrown-objects.md`, "What happens on a hit"); here K + 1 is S + 1,
  in the wait, and from S + 34 on the walker has been hit.
- **The walker** walks as usual on S, and its `vx` becomes −4 whichever
  way the player faces (d = +1 in `thrown-objects.md`, "The walker").
  It doesn't move from S + 1 to S + 33; it takes its K + 1 step there
  (including the turn at the end of its patrol) on **S + 34** and its
  K + 2 step on S + 35, then goes on as there.

Measured on the first level with the lead alone, in copies that put
this object at the start of the opening floor and a large, medium or
small walker in place of the first walker:

- by inputs: 240 runs of 500 frames (80 with each kind), carrying the
  object with Action held all the time and walking, turning, standing
  and jumping at random. 109 breaks on walkers (55 large, 54 medium):
  every one on the frame the rule gives, and the rule gave no hit in
  any other run. In each of the 108 that went on long enough, the
  character appeared at the object's carried position, neither the
  player nor the walker moved from S + 1 to S + 33, the walker's steps were as above (6 of them turned at the end of the
  patrol on their K + 1 step), and the player wasn't hurt from S − 1 to
  S + 80. In the other runs the object left the hands otherwise: the
  player was hurt (51 runs: 48 by small walkers, 3 by a medium or large
  walker from behind), bounced off the walker (56) or reached the
  barrier at the floor's left end (14); 10 runs ended still carrying;
- controlled: the 63 336 placements of `walker-contact.md` ("Carrying")
  for all three kinds, compared with the same placements carrying an
  object that holds nothing. The walker met the object on exactly the
  same placements (12 856 large, 8 288 medium, no small), and the player
  was hurt on none of them.

### The thrower and its balls

The object breaks on the thrower and on its balls (`thrower.md`) too.
For these, compare whole pixels: **I** is the object's `x` minus the
target's, both rounded down, and **H** is the target's `y` rounded up
minus the object's `y` rounded up (positive when the object is higher).
It breaks on a frame when I and H are both within these ranges (ends
included):

| Target | t from 1 to 16 | | t of 17 or more | |
|---|---|---|---|---|
| | I | H | I | H |
| the thrower | −55 to 37 | −47 to 65 | −30 to 36 | −23 to 32 |
| a ball | −47 to 27 | −52 to 50 | −21 to 27 | −25 to 20 |

Measured with the object moving right only, the thrower facing left on
its ledge, and a ball thrown straight up and falling; the ranges didn't
depend on the object's speed (3.5 to 12.7 px per frame). On two frames
(t = 15 and 16) the early range of I for the ball was −46 to 28 instead.

- **A ball** it breaks on is gone on S.
- **The thrower** is knocked out: it doesn't move from S to S + 33; on
  S + 34 its `vx` becomes 0 and its `vy` −5 (−1280/256) without moving;
  from S + 35 its `vy` increases by 7/16 every frame and it moves by it,
  as in `ground-action-enemies.md`.

### Everything else waits

From S + 1 to S + 33, while the character joins, the lead (as in
`following.md`), every walker (hit or not) and the thrower don't move;
from S + 34 they go on from where they were. (Walkers not hit were seen
to walk again on S + 34, in 7 throws.)

## Before it is thrown

- **Carried**: a medium or large walker that meets it breaks it
  ("Carried into a walker" above). Otherwise it doesn't break. A walker
  that reaches the player without meeting it (any small walker, or a
  medium or large one from behind) hurts the player as usual
  (`walker-contact.md`; with the lead alone, a knock-out), and the
  object is let go without breaking and falls away, still holding the
  missing character (`knock-out.md`, "An object in the hands").
- **During a throw** (Q to Q + 21, still in the hands): on **Q + 1 to
  Q + 20** a walker breaks it, at its position in the hands, by the
  ranges for t from 1 to 16 (measured: small walker facing left, object
  thrown right, I from −48 to 28 on each of those frames, with U from 39
  to 54). Not on Q, and not on Q + 21. The walker is hit as above.
  Seen in 4 throws by inputs, and on about 1 100 controlled frames
  moving the walker next to the object.
- **Carried up to a barrier**: the barrier doesn't break it. The lead
  is stopped by the step (`steep-faces.md`, "Stopped by a rise"), but
  the object held out in front reaches beyond it. In the one run, the
  exit at the level's left end then took over the lead, still on the
  step's lower side, and let the object go as in `exits.md` ("An object
  in the lead's hands"); it fell through the floor, was removed below
  the picture and **nobody joined**.
- **Bouncing on a walker while carrying** (`bounce.md`): the object
  leaves the hands and flies on ahead, then breaks by the rules above;
  in the runs seen (4, all the same jump), it broke on its first touch
  of the floor, 19 frames after the bounce and 85 px ahead. How it moves
  after leaving the hands this way isn't covered.
- **Put down** (`carrying.md`): it doesn't break, and it is then an
  object lying on the floor.

## Lying on the floor

An object lying on the floor (before it is picked up, or after it is
put down) doesn't break, and nothing happens to it: a walker walked
through it, and the player walked past it, rolled through it (5 rolls)
and jumped onto and over it (5 jumps), landing on the floor beneath it.
An Action press within reach picks it up (`carrying.md`). It doesn't
break when it leaves the picture either: one left lying behind the
player was not seen to free anyone.

## With both characters there

Such an object can also be met with both characters there: the holders
are back at their places after a fall-out with both characters there
(`restart-barrel.md`, "What is there again after a fall-out"), and one
holder broken still leaves any other one in the level. Then:

- It is picked up, carried and thrown as above, and a thrown one moves
  and breaks by the rules above, on the same frame S and for the same
  causes.
- On S **nobody comes out**: no character appears, the lead and the
  follower stay the same characters, and the lives and the count don't
  change (`following.md`, "Joining").
- **Nothing waits**: "Everything else waits" doesn't apply to the lead
  or the follower. From S + 1 on, the lead moves by its input as on any
  other frame, and the follower goes on following (`following.md`).

Measured on the first level at the second such object, with both
characters there after a fall-out and the resume at the restart point,
the first character leading or (after a swap) the second: 140 throws by
inputs (thrown from beside the object, carried left 30 frames and
thrown left, or thrown right after turning to it), with nothing, Right,
Left or Jump held after the throw. All 548 flight frames and the 140
breaks (on the floor and against a wall) matched the rules above, and on
S and the 40 frames after it the follower was still following. With Right
held after the throw, the lead moved within S + 1 to S + 33 in all 35
throws. The same throws and inputs with the lead alone at the same place
(96 throws, each character leading) had the missing character joining
and the lead still from S + 1 to S + 33 in every one. In 15 more throws
(both characters, each leading) the leading character, the lives and the
count were the same on every frame from the Action press to 200 frames
after the throw.

## Examples

Computed from the rules above. Thrown to the right while standing on
flat floor, as in `thrown-objects.md` "Example" (`x` and `y` from the
object's position on Q + 21, floor at `y` = 54, speeds in 1/256 px per
frame):

| Frame | `x` | `y` | `vx` | `vy` | |
|---|---|---|---|---|---|
| Q + 21 | 0 | 0 | 4096 | 256 | leaves the hands |
| Q + 22 | 14.4375 | 1.4375 | 3696 | 368 | t = 1 |
| Q + 33 | 111.9296875 | 46.125 | 1543 | 1600 | |
| Q + 34 | 117.64453125 | 54 | | | new `y` 52.8125 touches with `vy` 1600 before the frame: S; the character appears at (117, 54) |

**A wall.** The same throw with a face rising to the right whose first
column is at `x` = 60, floor at `y` = 4: on Q + 26 the move would take
`x` from 50.2109375 to 60.125, into that face column, higher than the
object's `y` 8.375: S. Its `x` stays 50.2109375 and its vertical move
still happens (`vy` 816, `y` 11.5625), so the character appears at
(50, 12).

**A walker.** The throw of `thrown-objects.md` "Hitting walkers",
"Example" (small walker facing left at `x` = 160.25 on Q + 21): the walker
is hit on Q + 32 with the object at (105.90234375, 39.875), so S =
Q + 32 and the character appears at (105, 40). The walker's `vx` becomes
−4 on S; it stays at `x` 149.25 until S + 33; on S + 34 its `vx` is
−960/256 and it moves to 145.5 with `vy` −6.

## Not covered

- How the object moves after a bounce makes the player let go of it.
- Carried into a walker: walkers on another floor or on a slope,
  walkers that are turning or speeding up, H lower than measured for a
  carried object (`thrown-objects.md`), and whether the player goes on
  from S + 34 with exactly the position and speeds it would have had
  still carrying (only that it doesn't move in the wait was checked).
- Throwers and balls with the object moving left, balls moving sideways,
  other enemies; walkers other than the small walker facing left with
  the object moving right (the ranges of `thrown-objects.md` are assumed
  to hold, as they did for that one).
- The barrier (case 2, a vertical step): heights more than 55 px above
  the floor there, and a barrier met moving right.
- Two causes on the same frame, other than the floor and the window
  together (seen once: the character appeared on the floor, `y` = `F`).
- The view moving on the frame the object leaves the picture (the window
  is taken from the view as in `thrower.md`, but only a still view was
  measured).
- With both characters there: walkers and the thrower from S + 1 to
  S + 33, breaks on enemies or at the edge of the picture, and the
  first such object (only the second was measured).
- A face column more than 8 px higher than the object: an object
  holding nothing breaks there too (`thrown-objects.md`, "Breaking
  against a wall"), but this object wasn't measured against one.
- Objects holding the missing character in other levels.
