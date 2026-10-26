# The metal barrel

Titles: `title-1`

The metal barrel is the object a `hidden-metal-barrel` leaves behind
(`hidden-objects.md`). It is picked up and carried as in `carrying.md`.
This file says what it does once it is thrown: how it flies, lands and
rolls, what it does at a wall, that it defeats walkers and stingers,
and what it does with the two characters (passing through, catching and
carrying them), to the thrower and its balls, and that it passes
critters. The other things a thrown object can meet (other enemies)
are not covered here; see "Not covered".

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx`
and `vy` are as in a trace: right and down are positive, speeds in px
per frame and written both ways, as in `thrown-objects.md` (896/256 =
3.5 px per frame). Q is the frame Action is let go, as in `carrying.md`.

## Like a barrel

From Q + 21, a thrown metal barrel moves exactly as `thrown-objects.md`
and `carrying.md` say for a barrel that holds nothing:

- the throw, its speed and how the speed settles (the speed rule), in the
  air and on the floor;
- falling, bouncing and rolling ("In the air", "The floor of a column",
  "Rolling"): it rolls at 3.5 px per frame and never stops on its own;
- slopes, ground passable from below, and the 8 px limits;
- being removed once it is far enough outside the picture ("Leaving the
  picture"), with the same limits and the same frame.

Where the two differ is a wall.

## Turning back at a wall

A **wall frame** is a frame on which a barrel would break: the move would
take the object into a face column, or into a column whose floor is more
than 8 px higher, as `thrown-objects.md` describes it under "Breaking
against a wall", "A floor more than 8 px higher" and "Breaking at a
vertical step". The conditions are the same for the metal barrel, in the
air and on the floor, going left or right, and an object below the
height limits there is removed as a barrel is.

On a wall frame the metal barrel **does not break**. It

- does not move horizontally: its `x` is its `x` at the start of the
  frame rounded down to a whole pixel, so a fraction of a pixel is lost;
- gets `vx` = 3.5 px per frame (896/256) **away from the wall**,
  whatever its speed was before (11.7, 8 and 1.6 px per frame were all
  measured, and all left at 3.5);
- goes on in `y` as it would have: the same fall, bounce or roll on
  that frame and the frames after it as for the barrel when it isn't at
  a wall.

From the next frame it moves away from the wall at 3.5 px per frame and
is an ordinary thrown metal barrel again: it falls if the floor is lower,
rolls on, and turns back again at the next wall it meets. It was seen
coming back from a wall at the left end of the first level, and rolling
on through slopes and past where it was thrown.

### Example

Synthetic. A step: the floor is at `y` = 300 for columns 112 and to the
right, and 64 px higher (`y` = 236) for columns 111 and to the left. A
metal barrel rolls left on the floor at `y` = 300 with `vx` = −3.5 and
`x` = 115.75 at the start of frame 1.

| Frame | `x` at end | `vx` at end | Notes |
|---|---|---|---|
| 1 | 112.25 | −3.5 | moves 3.5; stays in column 112 |
| 2 | 112 | +3.5 | the move would end in column 108, whose floor is more than 8 px higher: wall frame; no move, fraction dropped, turns |
| 3 | 115.5 | +3.5 | moves away |
| 4 | 119 | +3.5 | |

A barrel in the same place breaks on frame 2.

## Evidence

Measured on the first level with the first character alone. With a
setup method, a metal barrel stood at three places on the first level:
on flat floor near the middle of the level, at the foot of the 64 px step
at the level's left end, and on a floor with faces rising to the right
and to the left further on. No metal barrel is there in ordinary play,
so **none of the rules above has been seen in ordinary play**; every
situation below was arranged, with the barrel on or above the floor
where a thrown object can be. Every throw was repeated with a plain
barrel in the same place and the same inputs as a control.

- 650 throws by inputs alone (Action let go on frames 25 to 70 of the
  pick-up, walking, standing, turning and jumping either way): 518
  matched the control on every frame (`x`, `y`, `vx`, `vy` and the frame
  it was removed) to the end of the run. In 129 the control broke on
  one frame and the metal barrel turned on that same frame, as above:
  `x` unchanged but for its fraction, `vx` = ±3.5 px per frame away
  from the wall; 38 of them moving right and 91 moving left, at five
  different faces (the 64 px step at the first level's left end, and
  four faces of 4 px per column). No other difference was seen except
  two throws released inside a wall (see "Not covered") and one run
  that ended early.
- 2 275 starts at the 64 px step at the first level's left end: the
  metal barrel's `x` from 111 to 117 in steps of 0.25 (25 values) and
  its height from 0 to 90 px above the floor's top in steps of 1 px
  (91 values), going left at 3.5 px per frame. On 1 611 the control
  broke, and on every one the metal barrel turned on the same frame; on
  the other 664 both did the same.
- 576 starts near the same step: `x` 112, 113 and 114, 16 heights from 0
  to 60 px above the floor's top in steps of 4 px, `vy` −3, 0 and +3 px
  per frame, and `vx` −11.7, −8, −3.5 and −1.6 px per frame. In 462 the
  wall was met on the first frame, and `vx` was +3.5 on that frame
  whatever the starting speed. In 60 the object slowed and met the wall
  on a later frame, and in 54 it was removed first (above the height
  limits); these were not compared further.

## Walkers

A thrown metal barrel defeats the walkers it touches (`walker.md`) in
the same way as a thrown barrel does (`thrown-objects.md`, "Hitting
walkers"), for small, medium and large walkers, with the walker heading
either way and the object thrown either way, when it is thrown from the
flat floor at a walker standing on that floor ahead of the thrower. On
the frame of a hit the walker's knock-off (its speed, rise and fall) and
its removal are the same as for the barrel, and the metal barrel goes on
as if the walker weren't there, as the barrel does. Nothing in the
object's own motion differs from the barrel's when a walker is hit. Other
heights and throws made in the air, other floors, and a hit just after
the barrel turned back are not covered (see "Not covered").

- Walkers, by a setup method: 204 throws left or right from the flat
  floor in the first level (about 230 to 545 px along the level), each
  once with the metal barrel and once with a plain barrel, same inputs.
  A small, a medium or a large walker, heading left or right, stood
  ahead of the thrower on the same floor at distances of 46 to 238 px
  in steps of 12 (17 distances, 3 kinds, 2 headings, 2 throw
  directions). In 177 of the
  204 pairs every row (the walker's `x`, `y`, speeds and removal, the
  object's `x`, `y`, speeds and removal frame) was the same; the walker
  was knocked off in 142 of those 177, in every kind and both throw
  directions, and the other 35 had it out of reach. The other 27 pairs
  differ only in the object, which was overlapping a wall at the
  release (see "Not covered"); they were not compared further.

### Rolling right on the flat floor

A metal barrel rolling right at 3.5 px per frame on a flat floor, long
after it left the hands (t of 17 or more in `thrown-objects.md`, "When a
walker is hit"; a barrel turned back at a wall has t well past that),
defeats a small, medium or large walker walking on the same floor by the
ranges of that table's "t of 17 or more" column for an object moving
right, with the walker facing either way, and the height limits there
(the barrel and the walker are level, which is inside them). On the
frame of the hit the walker's sideways speed is 4 px per frame to the
left, whichever way it was facing. The barrel is not slowed, lifted or
removed by the hit; its `vx` stays 3.5 and its `y` stays on the floor
for the frames followed (70, up to its removal at the edge of the picture).

- Rolling, by a setup method: the barrel thrown left at the 64 px step
  and turned back by it, then rolling right on the flat floor. With the
  barrel rolling right on the floor at `x` of 200 or more, a small, a
  medium or a large walker facing left or right stood at the barrel's
  `x` plus an offset from −20 to 118 px in steps of 3 (47 offsets,
  3 kinds, 2 facings: 282 runs of 70 frames, counted from the first
  frame with the walker standing there). The first frame on which `x`
  (rounded down) of the barrel minus that of the walker was inside the
  "t of 17 or more" range for the kind and facing, after that first
  frame, was the first frame on which the walker was hit, in all 282
  runs; the walker's sideways speed on that frame was −4 px per frame in
  every one. In all 282 the barrel kept `vx` 3.5 and its floor `y` on
  every frame until it was removed, hit or not. It was removed (left the
  picture) on the 61st frame in 278 runs and was still there on frame 70
  in 4 (a medium walker 14, 17 or 20 px behind the barrel and a large one
  20 px behind it), so the claim covers the frames followed. (Those 4
  are the runs in which the player was hurt, below.)

The walker's frames after the hit are those of `thrown-objects.md`, "The
walker", with d = +1 (the walker is knocked left): on K its sideways
speed is −4 px per frame; on K + 1 it is −960/256 px per frame (or
−832/256 if its heading turned on K + 1 at the end of its patrol); from
then on its speed steps toward 2 px per frame in its heading, and its
height changes by the same amounts per frame as there, until it is
removed. The hit changes nothing else in the barrel's frames.

- Knock-off after a barrel rolling right, in the runs above: in the 278
  runs in which the player was not hurt, the walker's sideways speed on K
  was −4 px per frame, and its sideways speed, position and height on
  every frame from K + 1 to the last it was there were the rule's:
  10 067 frames (251 runs of 40 frames from K + 1 and 27 of 1 frame;
  small 94 runs, medium 91, large 93; the
  heading turned on K + 1 in 18 of the large walkers, in none of the
  others). The walker was there for the last time on K + 40 in 251 runs
  and on K + 1 in 27. In the other 4 runs (a medium walker 14, 17 or 20
  px behind the barrel, a large one 20 px behind it, all facing left)
  the walker, close to the player, hurt the player on K + 1 and the game
  stood still from then on; they are not claimed.

### Rolling left on the flat floor

A metal barrel rolling left on a flat floor, long after it left the
hands (29 or more frames, so "t of 17 or more"), defeats a small, medium
or large walker walking on the same floor by the ranges of the "t of 17
or more" column for an object moving left, with the walker facing either
way, and the height limits there (the barrel and the walker are level,
which is inside them). The walker is hit on the first frame on which
the difference is in the range for its facing on that frame, except in
the case listed under "Not covered" (a walker that appears right at the
range's upper end). On the frame of the hit the walker's sideways speed
is 4 px per frame to the right, whichever way it was facing. The barrel
is not slowed, lifted or removed by the hit: its `y` stays on the floor
for the frames followed and its `vx` is the same in every run, hit or
not (see the evidence).

- Rolling left, by a setup method: the barrel thrown left from the flat
  floor and the thrower then walking left behind it, so that the barrel
  stays in the picture (the barrel left the hands 29 frames before the
  first frame followed, and was rolling on the floor from then on). With
  the barrel rolling left on the floor at `x` of 340 (`vx` −3.77 px per
  frame), a small, a medium or a large walker facing left or right stood at the barrel's `x` plus an offset from
  −100 to −20 px in steps of 1 (81 offsets, 3 kinds, 2 facings: 486 runs
  of 61 frames, counted from the first frame with the walker standing
  there). The walker was hit in all 486, with sideways speed +4 px per
  frame on the frame of the hit. The first frame on which `x` (rounded
  down) of the barrel minus that of the walker was inside the range for
  the kind and facing, after that first frame, was the first frame on
  which it was hit in 481 runs. In 4 (a large walker facing right, the
  difference first inside the range at its upper end, 26, on one of the
  first five frames after the walker stood there) the hit came on a
  later frame, at a difference of 21 or 22. In 1 (a large walker facing
  left, 100 px ahead) the walker turned round at the end of its
  patrol one frame before the hit (the turn of `walker.md`) and was hit
  on the next frame, with the difference in the range for the new
  facing. In all 486 the barrel was still there at the end of the 61
  frames, with `y` on the floor and `vy` unchanged on every frame, hit or
  not (`x` from 336.5 to 124.5; it did not reach the left end of the
  floor). The barrel's `vx` was the same on every frame in all 486 runs,
  hit or not: −3.77 px per frame (−966/256) on the first frame followed,
  its size falling over the next 21 frames to 3.53 (−903/256) and
  then −3.5 px per frame (−896/256) from the 23rd frame on.

The walker's frames after the hit are those of `thrown-objects.md`, "The
walker", with d = −1 (the walker is knocked right): on K its sideways
speed is +4 px per frame; on K + 1 it is +960/256 px per frame (or
+832/256 if its heading turned on K + 1 at the end of its patrol); from
then on its speed steps toward 2 px per frame in its heading, and its
height changes by the same amounts per frame as there, until it is
removed. The hit changes nothing else in the barrel's frames.

- Knock-off after a barrel rolling left, by a setup method: the same
  arrangement as above, but with the walker standing at the barrel's `x`
  plus an offset from −100 to −10 px in steps of 3 (31 offsets, 3 kinds,
  2 facings: 186 runs), with the barrel rolling left on the floor at `x`
  of 440 on that frame (so that the walker stays in the picture longer).
  The walker was hit in all 186, with sideways speed +4 px per frame on
  K. In all 186 the walker's sideways speed, position and height on
  every frame from K + 1 to the last it was there were the rule's, with
  no exception: 4 515 frames (111 runs of 40 frames from K + 1 and 75 of
  1 frame; 62 runs of each kind). The heading was not turned on K + 1 in
  any of them, so the +832/256 step was not seen for a barrel rolling
  left. The walker was there for the last time on K + 40 in the 111 runs
  in which its `x` on K, relative to the picture's left edge, was −39.6
  to +9.4 px, and on K + 1 in the 75 in which it was −64.6 to −39.6 px
  (further left; the value −39.6 occurs in both groups, so the edge is
  not claimed more closely than that). The player was not hurt in any
  run.
- The same check for a barrel thrown left by the second character (the
  runs of "The second character throwing it" that hit a walker, 478): on
  K the sideways speed was +4 px per frame in all 478; in 462 the walker was
  there for the last time on K + 1, and in the other 16 on K + 39, with the rule's speed, position and height
  on every frame (no exception). The heading did not turn on K + 1 in
  any of the 478.

## The second character throwing it

Thrown by the second character (`second-character.md`, `carrying.md`,
"The object, second character"), a metal barrel behaves as follows,
from the frame it leaves the second character's hands (it leaves the
hands on Q + 18 and first moves on Q + 19, as in `thrown-objects.md`):

- for the throws measured (below; slopes were not singled out), until the frame it meets a wall it
  moves row for row as a plain barrel thrown by the second character
  does, in `x`, `y`, `vx`, `vy` and the frame it is removed;
- on a wall frame it turns back as in "Turning back at a wall": `x` is
  the whole part of its `x` before the frame and `vx` = 3.5 px per frame
  (896/256) away from the wall. Its `y` after that frame was not
  compared with a plain barrel (the plain barrel is broken there);
- the second character's own motion and timing during the pick-up, the
  carry and the throw are the same as with a plain barrel (pictures were
  not compared).

A walker reached by the metal barrel thrown by the second character,
after a carry on flat floor, with the walker on the same flat floor
ahead of the object, is defeated exactly
as when a plain barrel hits it (`thrown-objects.md`, "Hitting walkers";
the second character's frames are in `carrying.md`): the walker's rows
and the second character's rows are the same as for the plain barrel,
for small, medium and large walkers heading either way, the object
thrown either way. When the walker reaches the second character while
the object is still in the hands, the object is not broken: it stays,
with `vx` 0 and `vy` −5 px per frame (1280/256, up) on the first frame
that differs from a plain barrel (which is removed), as for a metal
barrel left in the hands of a hit thrower ("The thrower and its
balls"). It was followed for 37 frames, until it was gone; what it does
between is not described (see "Not covered").

The ledge thrower (`thrower.md`) reached by the second character's metal
barrel is knocked out on the same frame as by a plain barrel, and the
thrower's rows and the second character's rows are the same as with a
plain barrel on every frame. A barrel still in the second character's
hands when it hits the thrower is not broken, as for the first
character ("The thrower and its balls"): on the frame of the hit it is
kept with `vx` 0 and `vy` −5 px per frame (up), then falls straight down
through the ledge's floor, and was removed 40 or 41 frames after the hit
(46 for one carried, not yet thrown). In the arrangement measured (below)
no barrel that had left the hands hit the thrower.

The thrower's balls (`thrower.md`) are removed by the second character's
metal barrel in exactly the places where a plain object held or thrown
by the second character removes them, and what happens to the metal
barrel is as for the first character ("The thrower and its balls"): one
that is thrown goes on as the plain one does, and one in the hands or
carried is kept, with `vx` 0 and `vy` −5 px per frame (up), where the
plain one breaks. What the second character does is the same as with a
plain object on every frame measured.

### Evidence for the second character's throw

Measured on the first level with the second character leading, in
arranged situations only: a setup method started the second character at
the start and made a metal barrel available ahead on the flat floor (no
metal barrel is there in ordinary play). Every run was played twice with
the same inputs, once with the metal barrel and once with a plain object
in the same place as a control.

- 600 throws by inputs (walking, standing and jumping either way while
  carrying, Action let go with Left, Right, Up or nothing held, then
  random inputs in flight, 200 frames or fewer): 285 matched the control
  on every frame of the object and the second character to the end; in
  all 285 the control was removed outside the picture before the end
  (whether the flat floor only was met was not recorded). In the other
  315 the control broke at a wall (the 64 px step at the left end of the
  level) on one frame, with the object moving left;
  on the frame before, its `y` speed was upward in 46 and zero or
  downward in 269, and whether it was rolling or in the air was not
  recorded. On that frame the metal barrel kept an `x` equal to the whole
  part of its `x` before, with `vx` +3.5 px per frame (896/256) in all
  315, and was still there 10 frames later in all 315; the runs were not
  followed further. Before that frame the object's rows matched the
  control in every run, and the second character's rows matched on every
  frame.
- 576 throws at a walker, by inputs, on flat floor, for small, medium and
  large walkers heading left or right, thrown left or right, Action let
  go after a carry of 12, 17 or 22 frames, the walker standing 40 to 160
  px (steps of 8) ahead of the thrown object on the frame before the
  release; whether the object met the walker in the air or rolling was
  not recorded, and no throw was made in the air. In all 576 the
  walker's and the second character's rows were equal to the control's
  on every frame. The object's rows were equal in 510.
  In the other 66 (walker 40 to 64 px ahead) the walker reached the
  second character with the object in its hands: the plain object was
  removed and the metal barrel was kept as above, and was gone 37 frames
  later in all 66.

- The thrower: 102 pairs of runs of 70 frames after 40 frames of
  standing, played with the metal barrel and with a plain object in the
  same place (a setup method was used for the start; the second
  character led and stood on the thrower's ledge with an object in its
  hands, at 110 px left to 40 px right of the thrower in steps of 3 px,
  51 positions, and then threw it left, the way it faced, or right
  after turning, by inputs). The thrower's rows
  and the second character's rows (`x`, `y`, speeds, action) were the same in the
  metal run and its control on every frame in all 102 pairs. In 44 pairs
  (positions from 26 px left to 37 px right of the thrower) the second
  character landed on the thrower on the first frame and the
  thrower was knocked out; the object, still in the
  hands, was thrown as the control was. In 15 pairs the object hit the
  thrower before it left the hands: 7 thrown left (positions 29 to 47
  px left of the thrower, hit 1, 3 or 6 frames after Action was let go) and 6
  right (32 to 47 px left, hit 5 or 6 frames after it), and 2 (one per
  direction) at 40 px right with the object carried, hit on the first
  frame. In those 15 the plain object was removed on the hit frame and
  the metal barrel was kept as above; it was removed 41 (5 runs), 40 (8
  runs) and 46 (2 runs, carried) frames after the hit. In the other 43 pairs
  (all 110 to 50 px left of the thrower, and one at 29 px left
  thrown right) the thrower was not hit and the metal and control rows
  were the same. No barrel that had left the hands hit the thrower.
- Balls, with the second character leading and holding the object, one
  frame at a time with a setup method (not reachable in play): 17 grids
  of 6 561 positions of a ball beside the object (offsets from −80 to
  80 px in `x` and −75 to 85 px in `y` in steps of 2), 111 537 positions
  in all, each played with the metal barrel and with a plain object. The
  ball was one the thrower had thrown. The frames were 7 after a throw to the left and 7 after a
  throw to the right (1, 10, 20, 22, 37, 38 and 51 frames after Action
  was let go; the object is still in the hands on the first two and has
  left them on the others, as it leaves them 18 frames after Action is
  let go) and 3 with the object carried: standing right after the pick-up,
  standing 20 frames later, and 8 frames into a jump. The ball turn
  parts (`ball-contact.md`) were: first in 5 grids, second in 5, third in
  3 and fourth in 4. In the 6 grids
  with the object thrown 37, 38 and 51 frames after Action was let go
  (3 to the left, 3 to the right) the ball was also removed in 14 036
  positions without the object there (it fell out of the picture or into
  the floor); those were left out. In the other positions the ball was removed with the object there
  in 19 288: 8 208 left, 9 401 right and 1 679 carried. The ball
  was removed in the same positions with the metal barrel and with the
  plain object in every grid, and the second character's position,
  speeds and action were the same in both runs in all 111 537. The
  object was in the hands or carried in 9 479 of the removals (the plain
  one broke, the metal barrel was kept with `vx` 0 and `vy` −5 px per
  frame) and thrown in the other 9 809 (the metal barrel's `x`, `y` and
  speeds equal to the plain object's). The only grid that removed
  no ball with the object there was the thrown-left one 51 frames after
  Action was let go. The positions removed were not compared with the
  table of ranges.

### Rolling left, thrown by the second character, against walkers

A metal barrel thrown left by the second character (it leaves the hands
on Q + 18 and first moves on Q + 19, as above) and rolling left on the
flat floor, 3.67 to 3.74 px per frame (939/256 to 958/256) on the frames
where a walker was hit, hits a small, medium or large walker walking on
the same floor on the frame after the first one followed, when the
difference (whole part of the barrel's `x` minus that of the walker) was
in the range for its kind and facing on the first frame followed and is
still in it, the ranges being
those of the "t of 17 or more" column for an object moving left in
`thrown-objects.md` ("When a walker is hit"). On the frame of the hit the
walker's sideways speed is 4 px per frame to the right whichever way it
faced. The barrel is not slowed or lifted: its `y` stays on the floor and
its `vx` on each frame is the same whether a walker is hit or not.

Only the lower end of each range was reached (the difference at which the
walker is behind the barrel); the upper ends are not covered, because the
floor was too short for a walker to stand far enough ahead. Every hit
measured was of a walker already in range on the first frame followed, hit
on the next frame; a walker that comes into the range from outside it (none
of the hit runs had one), and so whether a hit can come on the first frame
in range, is not covered, and neither are the walker's frames after the
hit (see "Not covered").

#### Evidence for the second character's barrel against walkers

Measured on the first level with the second character leading, in
arranged situations only: a setup method started the second character at
the start and made a metal barrel available ahead on the flat floor (no
metal barrel is there in ordinary play), and a walker stood on the floor
at a chosen `x` on the frame the barrel was first rolling left past a
chosen `x` (the barrel's `x` on that frame was 122.4 to 133.5 px). That
frame is the first one followed in each run.

- Small, medium and large walkers, each facing left or right, standing at
  `x` from 114 to 175 in steps of 1 (62 values), the barrel past 125 or
  past 135: 744 runs of 40 frames. Only the frames on which the barrel
  was moving left were checked: 2 580 in all, 3 in each of the 372 runs
  with the barrel past 125 and 3 (256 runs) or 6 (116 runs) in the
  others, the same for every run of a set; the barrel turned back at the
  wall after them.
- Walker hit in 478 of the 744; in the other 266 it was not hit while the
  barrel moved left. In all 478 the hit came on the second frame followed,
  never on the first, and the difference on it was in the range for the
  walker's kind and facing; in the other 266 no frame after the first had
  it. On the first frame followed the difference was in range in 526 of
  the 744 runs (the 478 hit ones and 48 not hit, whose difference had left
  the range by the second frame); 0 exceptions to the rule. The smallest
  difference at a hit was −28 (small, facing left), −27 (small, right),
  −35 (medium, left), −25 (medium, right), −31 (large, left) and −30
  (large, right), each the lower end of the range; the largest was 16, so
  the upper ends (20 to 30) were not tried.
- On the frame of the hit the walker's sideways speed was 4 px per frame
  to the right in all 478, whichever way it faced.
- In all 744 runs the barrel's rows (`x`, `y`, `vx`, `vy`) on the frames
  checked were equal to those of every other run with the same starting
  position, hit or not, with `y` on the floor on every frame.
- The walker's frames after the hit were not compared with the walker
  rules, and the barrel's frames after it turned back at the wall were
  not followed.

### Rolling right, thrown by the second character

A metal barrel thrown right by the second character, once it has landed
and is rolling right on the flat floor (the first frame followed was
37 frames after it left the hands, so "t of 17 or more" in
`thrown-objects.md`, "When a walker is hit"), defeats a small, medium or
large walker on the same flat floor by the ranges of that table's "t of
17 or more" column for an object moving right, as for the first
character's barrel ("Rolling right on the flat floor"), with the walker
facing either way and the barrel and walker level. The walker is hit on
the first frame, after the first one followed, on which the difference is
in the range, and on that frame its sideways speed is 4 px per frame to
the left, whichever way it was facing. A walker already in the range on
the first frame followed was hit on the next frame (173 of the runs); the
first frame followed itself is not covered, because the walker was on
the floor from then. Two differences from the first character's rolling barrel
in the same situation:

- the barrel's speed is not 3.5 px per frame at first: it is 3.62 px per
  frame (926/256) on the first frame followed, falls by 3/256 or less
  per frame to 903/256 on the 15th frame, and is 3.5 px per frame
  (896/256) from the 16th on, through the 29 frames followed (the last
  on the slope). The hits were not different for it, apart from the
  next point;
- a large walker facing right was hit one pixel before the table's lower
  end, at a difference of −26 instead of −25, in 11 of 71 runs (barrel
  speed 906/256 to 923/256, 3.54 to 3.61 px per frame). Whether that is the barrel's speed
  or the throw by the second character was not separated. The lower ends
  for the other five kinds and facings are the table's.

The barrel is not slowed, lifted or removed by the hit: its rows are
the same as those of a run with no walker in its way. At the place
measured it is removed on Q + 84 (66 frames after it left the hands),
hit or not, once it has rolled past the flat floor's end onto the slope
and is 382 px right of the picture's left edge; where it is removed
elsewhere is not covered. The walker's frames after the hit are those of
"Rolling right on the flat floor" (`thrown-objects.md`, "The walker",
with d = +1), except that a walker that was hit more than 296 px right of
the picture's left edge (beyond its right edge) was gone one frame later
(281 runs), while one hit 277 to 296 px right of it stayed to the end of
the frames followed (117 runs).

#### Evidence for the second character's barrel rolling right

By a setup method, on the first level with the second character leading:
it picked the barrel up, walked right to a point where the flat floor
lay ahead, turned right and let go (the barrel left the hands on Q + 18);
the barrel flew, bounced and landed on the flat floor. The walker
stood on the floor from the frame on which the barrel's `x` was first at
least 700, at an `x` from 3 px behind the barrel's to 67 px ahead of it
in steps of 1 px (71 positions, 3 kinds, 2 facings: 426 runs of 41
frames; the barrel was in the picture on the first 29 and removed on
the 30th). The flat floor ends a short way beyond the last position (a
slope begins there, at a barrel `x` of 774), so the barrel rolled on the
flat floor over only about 20 frames, at 3.5 to 3.62 px per frame;
in 28 runs the walker, near the end of the flat floor, stood 1 to 5 px
above the barrel's floor on the hit frame and they are not claimed.

- Hit frame: in the 398 runs with the walker level with the barrel on
  the frame of its hit (small 70 facing left and 59 right, medium 70 and
  58, large 70 and 71), the first frame on which `x` (rounded down) of
  the barrel minus that of the walker was inside the range for the kind
  and facing, after the first frame followed (the hits came on the 2nd
  to the 15th frame followed, at barrel speeds of 903/256 to 923/256),
  was the first frame on
  which the walker was hit (sideways speed −4 px per frame) in 387; in
  the other 11 (large, facing right) the walker was hit one frame
  earlier, at −26. In all 398 the difference on the hit frame was from
  the range's lower end (−26 to −20 by kind and facing) up to 7; the upper ends of the ranges (25 to
  35) were not reached, because no walker stood far enough
  behind the barrel on level floor.
- Walker after the hit: in all 398, 4 844 frames from the one after the
  hit (the walker was last in the picture 39 frames after the hit, the end
  of the frames followed, in 117 runs, those hit 277 to 296 px right of
  the picture's left edge, and one frame after it in 281, those hit
  more than 296 px right of it), its
  speed, position and height on every frame were the rule's.
- Barrel: in all 426 runs the barrel's `x`, `y`, `vx` and `vy` on every
  frame in the picture (29 frames in each, 12 354 in all) were equal to a run of the same throw with the
  walker out of reach (the removal frame is not counted), and the second character, standing, was unchanged.

## The characters

Measured with the first character alone, on flat floor (and, for the
end of a ride, at one face), with a metal barrel **rolling right** at
3.5 px per frame on the floor; the barrel's `y` is the floor's. Rolling
left is in "A barrel rolling left" after this section's rules. Here `y` is the character's feet and down is
positive, as in a trace; "above" means a smaller `y`. The situations
were arranged (see "Evidence for the characters" at the end of this
section).

**On the floor, and rising.** A character on the floor, standing,
walking either way or crouching, is not touched by the barrel: it rolls
through the character, the character's pose does not change because of
it, and no hurt was seen. For a standing or crouching character `x` is
unchanged too; for walking, `x` was not compared with a run without the
barrel. A character who is rising in a jump is not caught either.

**Coming down onto it.** A character falling in the air is caught by the
barrel on a frame on which the fall would end with the character's `y`
17.5 px above the barrel's `y` or lower, while the horizontal distance
**D** is in range. D is the character's `x` minus the barrel's `x`, the
barrel's `x` being as it is at the end of that frame (the barrel moves
first). The character's `x` is its value on that frame; it does not
change during a fall that starts from standing still. On the frame
before the catch the fall would have ended 24.8 px above the barrel's
`y`, and the character was not caught then; where between 17.5 and 24.8
the limit lies was not measured. D is a fractional distance. The ranges
(px; not caught outside them):

| Character faces | Caught from D = | to D = | Not caught at D = | and at D = |
|---|---|---|---|---|
| left | −26.152 | 23.926 | −26.496 or less | 24.426 or more |
| right | −22.848 | 27.227 | −23.32 or less | 27.727 or more |

Every tried jump with D inside a range was caught (212 distinct
left-facing and 295 right-facing values of D were tried inside the
ranges, ends included; the gaps between neighbouring values were up
to 0.97 px on the left and 0.50 px on the right). So each end lies
between the last caught value and the first not-caught one in the
table, no closer.

A character who comes down outside the range lands on the floor as usual
and the barrel rolls through.

**Riding.** On the catch frame C the character's `y` becomes the
barrel's `y` − 16.5 (between 16.0 and 17.0 above in the 85 catches
checked for this, see below) and its `x` the barrel's `x` (at the end of
C) minus a distance between 7.3 and 8.2 px that differs from catch to
catch. The character is then riding. On C + 1 the character's `x` does
not change, so the barrel is 10.8 to 11.7 ahead of it; from C + 2 on, the
character moves with the barrel (3.5 px per frame, and the trace shows a
speed of 3.5 px per frame) at the same distance. The height wobbles by
less than 1 px from frame to frame; riding over slopes was not checked.
Left, Right, Down and Action do nothing while riding (Action does not
pick the barrel up); each was held for 10 frames, 10 frames after the
catch, and the ride was frame for frame the same as without the input.

**Jumping off.** A Jump press on frame C + 2 (tried up to C + 6) starts
an ordinary jump (`jump-arc.md`) from the riding height. On the press
frame `vx` is the barrel's 3.5 px per frame, and then it falls by one
eighth each frame (3.5, 3.06, 2.68, 2.35, …); the character comes down
onto the floor behind the barrel. A press that
begins on C + 1 or earlier does nothing and is not remembered (tried down
to C − 4), except that a press that began on C, C − 1 or C − 2 and is
still held on C + 2 starts the jump on C + 2, as if pressed then (held
presses that began earlier were not tried). One that begins on C + 1 and
is held does not jump (1 to 6 frames tried).

**The ride ends** when the character would walk into a wall or a face
that the barrel itself meets (the barrel turns on its own rules above):
the character is put on the floor at its own `x` in the same frame and
walks on with the ordinary rules. This was seen once, with the barrel
rolling right at a face that rises to the right. One ride was followed
for 913 frames, from the catch to that face; the barrel was not removed
while the character was on it, and no other end was seen in that ride.

**A barrel still in the air.** A metal barrel that is in the air after
a throw can also catch a falling character, with the same distances and
the same ride as a rolling one, as far as these were measured (below).
On the catch frame C the character's `x` becomes the barrel's `x` (at
the end of C) minus 7.5 to 8.0 px, and its `y` becomes 15.2 to 16.7 px
above the barrel's `y`. For a barrel moving left, the same holds
mirrored: the character is that far to the barrel's right (8.0 to 8.5
px). From C + 1 on the barrel is 11.0 to 11.5 px ahead of the character
(11.5 to 12.0 moving left), and from C + 2 the character moves with the
barrel at 3.5 px per frame, as in "Riding". Every catch was of a
character who was falling on that frame, with D (as above) from −23.0
to 23.5 px. Whether the ranges of D in the table above hold for a barrel
in the air was not measured, nor which falling characters are caught
(the height limit is in "Not covered"). A character on the floor or
rising was never caught by a barrel in the air, and nothing else
happened to it (no hurt, no change of pose or `x`).

With a character riding it, the barrel falls differently from one
without a rider: its downward speed grows by 0.3125 px per frame each
frame (once 0.3086), where the free barrel's grows by 0.4375 px per
frame each frame. In the 2 rides followed to the floor the barrel
landed, bounced once, and rolled on at 3.5 px per frame with the
character still riding at the same distance, for 50 frames and more.

All of this was seen only in the arrangement described in the evidence
below, with a barrel in the air at places that throws and jumps in
ordinary play do not obviously bring about; it was not shown to hold in
ordinary play.

**Picking it up.** A rolling metal barrel can be picked up with Action
from the floor, by a character who is not riding it; the pick-up
starts as for a barrel (`carrying.md`). Tried twice, with the barrel
37 and 60 px away when Action was pressed.

### A barrel rolling left

The same measurements were made with the first character alone and a
metal barrel **rolling left** on the floor at 3.5 px per frame (the
barrel having turned back at a face to the right of the character and
come back along the floor). `y`, "above" and D are as above, D being the
character's `x` minus the barrel's `x` at the end of the catch frame;
the character is to the right of the barrel when it is caught. The
situations were arranged (see "Evidence for a barrel rolling left").

**On the floor, and rising.** As for a barrel rolling right: a character
standing, crouching or walking either way is not touched, and a
character who is rising in a jump is not caught. The barrel's `x` came
within 0.5 to 2 px of the character's `x` in the runs where it reached
the character on the floor; the character's `x` (standing, crouching)
did not change while it went by, its pose stayed the one its inputs
gave, and no hurt was seen. The walking runs were not compared with runs
without the barrel.

**Coming down onto it.** A falling character is caught on the same
frame as for a barrel rolling right: the one on which the fall would end
17.5 px or less above the barrel's `y`, the frame before it being 24.8
px above. The ranges of D (px; not caught outside them) are not those of
the barrel rolling right:

| Character faces | Caught from D = | to D = | Not caught at D = | and at D = |
|---|---|---|---|---|
| left | −26.41 | 23.598 | −26.664 or less | 24.0 or more |
| right | −23.453 | 26.852 | −23.777 or less | 27.285 or more |

Every jump with D inside a range was caught (173 distinct values of D
inside the left-facing range, 141 inside the right-facing one; the gaps
between neighbouring tried values were up to 0.57 px on the left and
1.23 px on the right, so the ends are known to within the gaps shown
in the table, no closer).

**Riding.** As for a barrel rolling right, mirrored. On the catch frame
C the character's `y` becomes the barrel's `y` − 16.5 (16.53 in 128 of the 129
catches checked for this, 16.84 in 1), and its `x` the barrel's `x`
(at the end of C) plus a distance between 7.98 and 8.89 px that differs
from catch to catch. On C + 1 the character's `x` does not change while
the barrel moves 3.5 px to the left, so the distance becomes 3.5 px
larger (11.48 to 12.39 px). From C + 2 the character moves with the
barrel, 3.5 px per frame to the left, at that distance for the next 60
frames (the distance did not change from C + 2 in any of the 129
catches); the height above the barrel
stays between 16.0 and 17.0 px. The barrel's and the character's `vx`
are both −3.5 px per frame.

**Inputs.** Left, Right, Up, Down, Action, and Left with Action, each
held for 10 frames starting 10 frames after the catch, change nothing:
the ride and the barrel were frame for frame those of the run with no
input.

**Jumping off.** As for a barrel rolling right: a Jump press on C + 2
(also C + 3, C + 4 and C + 6) starts an ordinary jump with `vx` −3.5 px
per frame on the press frame, falling by one eighth each frame after it
(−3.5, −3.06, −2.68, −2.35, …). Every press tried was 6 frames long. One
begun on C − 4 (so over before C + 2) does nothing; one begun on C + 1
(over on C + 6) does not jump; presses begun on C − 2, C − 1 and C
(each still held on C + 2) jump on C + 2. Presses begun on C − 3, and
longer or shorter presses, were not tried.

**Picking it up.** With Action pressed while the barrel rolled toward
the character, the character picked it up (the pose of carrying) with
the barrel 36 px, 15 px and 1 px to its right and 13 px to its left
(4 runs). Farther away was not measured.

### Evidence for the characters

By a setup method a metal barrel was made available to pick up at a
chosen place on flat floor in the first level. From then on the character
stood at its own place and received inputs only: it picked the barrel up,
carried it left and threw it left (Action let go with Left held), so
that the barrel turned back at the wall and rolled right toward the
character. That arrangement is not seen in ordinary play.

- Catch ranges: 2 112 jumps, each a standing wait of 3 to 24 frames
  followed by a jump straight up (Jump held for 20 frames, nothing
  else), after pressing Left or Right for 1 to 12 frames to set the
  facing and the place: 12 press lengths × 2 directions × 4 barrel
  start places (2 px apart) × 22 waits, 1 056 per direction. Facing was
  taken at the jump, not from the direction pressed: a Right press of 1
  frame left the character facing left in all 88 such jumps, and of 2
  frames in 4 of 88. So 1 148 jumps were left-facing (481 caught) and
  964 right-facing (613 caught).
- Riding values: 85 further catches (left and right facing, 5 walk
  lengths, 3 barrel start places, 4 waits), each with the distance and
  the height on the catch frame, the next two frames and the ride after.
- A repeat of 100 of the 2 112 jumps (50 caught, 50 not) gave the same
  result on every one, every catch on a frame on which the character was
  falling, and no pose other than standing, walking, jumping, crouching
  or riding.
- Passing: 3 barrel start places for each of standing, crouching,
  walking right, walking left and jumping straight up (15 runs of
  about 120 frames). In the 11 standing, crouching and walking runs in
  which the barrel reached the character, the barrel's `x` came within
  0.7 px of the character's `x` at the closest; the character's `x` (when
  standing or crouching) and pose were those of the same inputs without
  a barrel, and no hurt pose appeared. The jumps in this list are not
  counted as rising evidence; the rising claim rests on the 2 112 jumps
  above, in none of which a catch came on a rising frame.
- Jump-off: presses from C − 4 to C + 1 (6 runs) did nothing and were not
  remembered; presses at C + 2 to C + 6 (5 runs) each started a jump.
  Jump held for 1 to 6 frames starting on C − 2 to C + 2 (30 runs): a
  start on C + 2 jumped on its first frame; a start on C + 1 never
  jumped; a start on C, C − 1 or C − 2 that was still held on C + 2
  (3, 4 or 5 frames or more) jumped on C + 2.
- Ride: one ride of 913 frames to the face; 4 runs holding Left, Right,
  Down or Action for 10 frames (and 2 with Jump).
- In the air, by a setup method: after a throw, a metal barrel was in
  the air at 80 px behind to 80 px ahead of the character (81 places, 2
  px steps), and the run was followed for 12 frames and compared with
  the character's run in the same inputs with no barrel near it. With
  the character standing on the floor: 4 grids of 1 458 placements (81
  places times 18 heights: level with the character and 2 to 66 px
  above it in 4 px steps), the barrel thrown right or left, the
  character facing it or turned away; 5 832 in all: no catch, no hurt,
  no difference from the run with no barrel. With the character jumping
  straight up (Jump held for 40 frames), 10 grids of 1 377 placements
  each (81 places times 17 heights, from 48 px below to 16 px above the
  character's height on that frame, in 4 px steps), by the number of
  frames after the Jump press and the facing: 6, 14, 22 and 30 frames,
  facing right with the barrel thrown right (4 grids, 50 frames of
  waiting before the Jump); 18, 22, 26 and 30 frames, facing
  right with the barrel thrown right (4 grids, 49 frames of
  waiting before the Jump, so the 22- and 30-frame grids of the two
  groups differ by one frame in when the Jump came after the throw); 18 and 22 frames, facing left with the barrel thrown left (2
  grids). A
  further 2 grids (26 and 30 frames, facing left) had the barrel
  outside the picture and gave no data. The 6- and 14-frame grids
  (rising) gave no catch. The other 8 gave 227, 201, 111, 235, 228, 191
  (facing right) and 77, 231 (facing left): 1 501 catches, 1 193 and
  308, every one on a frame on which the character was falling. The
  values above (distances on C, C + 1, C + 2, the barrel's 3.5 px per
  frame on C + 2, the fall speed with a rider) held in all 1 501. A
  character facing against the barrel's direction, in the air, was not
  tried.
- Pick-up: two runs (see above).

### Evidence for a barrel rolling left

By a setup method a metal barrel was made available to pick up on the
flat floor in the first level. On screen the floor ended, about 165 px
to the right of the character, in a face rising 32 px. The character picked the barrel up, carried it right
and threw it right (Action let go with Right held) at once. The barrel
flew to the face, turned back in the air, and rolled left along the
floor past the character, who stood where it had thrown from. That
arrangement is not seen in ordinary play. The character's inputs after
the throw were a wait, a press of Left or Right of 1 to 12 frames (to
set the facing and the place), a second wait of 0 to 22 frames, and a
Jump held for 20 frames.

- Catch ranges: 2 496 jumps in two grids: 2 208 (first waits of 40, 42,
  44 and 46 frames × 2 directions × 12 press lengths × 23 second
  waits) and 288 (first waits of 34, 36, 38 and 40 frames, Left only, press
  lengths 1 to 12, second waits of 0 to 5 frames). 1 084 were facing left at the jump
  and 1 412 facing right (facing was taken at the jump, not from the
  direction pressed: a short press leaves the facing the character
  had); 997 were caught. In all 2 496 the fall reached the catch
  height from 24.8 px above the barrel on the frame before.
- Riding values: 129 catches (72 facing left, 57 right): every seventh
  of the 907 caught jumps of the first grid (the second grid's 90 were
  not used), with the distance and height on C, C + 1,
  C + 2 and the extremes over the 60 frames after.
- Sideways speed at the jump: measured again for every combination of
  direction, press length (1 to 12) and second wait (0 to 22) with the
  first wait 40 frames: 552 combinations, which include the second
  grid's jumps at a first wait of 40 frames (Left only, press lengths 1
  to 12, second waits of 0 to 5). On the first frame the character rose it was still
  moving sideways in 542, in the direction pressed, from 0.03 px per
  frame (second wait 22) up to 0.94 px per frame (press of 12, second
  wait 0), less the longer the second wait; in 10 (a press of 1 frame,
  second waits of 18 to 22) it was not. These jumps are in the counts
  and ranges above, and every jump inside a range was caught and none
  outside, whatever its sideways speed; the ranges were not separated by
  it. The first waits of 34, 36 and 38 frames and of 42 to 46 frames were
  not measured again.
- Passing: standing, crouching, walking left, walking right and turning
  to the left (5 runs of about 200 frames), and 4 jumps that had the
  barrel come by while the character rose (56 rising frames with the
  barrel within 40 px, D from −28.5 to 38).
- Jump-off: one catch repeated with a Jump press of 6 frames beginning
  on C − 4, C − 2, C − 1, C, C + 1, C + 2, C + 3, C + 4 and C + 6 (9
  runs), the first jump of the run having been held for 20 frames.
- Inputs: the same catch, 6 runs, one button set each (as listed), and a
  run with none.
- Pick-up: 4 runs, Action held for 3 frames, at the distances above.

## The thrower and its balls

A ball (`thrower.md`) is removed by a metal barrel, and the thrower
(`thrower.md`) is knocked out by it, in exactly the places and on exactly
the frames where they are for a plain barrel (`thrown-objects.md`,
"Hitting a ball" and "Hitting the thrower", with the same ranges). For
the balls this was measured for a thrown object, one still in the
player's hands (Q + 1 to Q + 20) and one that is carried. For the
thrower it was measured for a thrown object (moving right or left), one
in the hands (Q + 1 to Q + 20, thrown right or left) and one carried
while the player jumps; see "Not covered" for the rest. The thrower's
knock-out, its removal and the player's frames are the same as with a
plain barrel.

What differs is what happens to the metal barrel when it is in the
hands or carried. A barrel that is hit there breaks; the metal barrel
does not:

- Its pieces do not appear; the flash of the hit does.
- It stays in the level with `vx` = 0 and `vy` = −5 px per frame (moving
  up) on the frame of the hit, and from then on falls as an object in the
  air falls (`vy` gaining 0.4375 px per frame, to 8 px per frame down). It
  keeps its `x`, apart from the move the throw gave it on that frame.
- It does not stop on the floor, even on the floor of the ledge it was
  held over, and falls straight down. It was removed 39 to 49 frames
  after the hit (47 and 48 for one carried in a jump), once it was about
  250 px below the top of the picture (250 to 254 on its last frame in
  the first set, 249 to 256 in the second).
- What it was held by is let go of as when a barrel breaks: a throw in
  progress goes on and a carried object is no longer carried (`x`, `y`
  and action of the player were the same as with a plain barrel for 70
  frames in four runs).

A metal barrel that was thrown (from Q + 22 on) is not touched by the
hit and goes on as a barrel does, as in `thrown-objects.md`.

### Evidence

Measured on the first level with the first character alone. With a
setup method, a metal barrel stood where the plain barrel stood in the
same situation (beside the ledge the thrower stands on, in the player's
hands). Every run was repeated with a plain barrel in the same place and
with the same inputs.

- The thrower: 102 pairs of runs of 70 frames. In each the player stood
  on the thrower's ledge (a setup method), from 110 px left to 40 px
  right of the thrower in steps of 3 px (51 positions), and then threw
  right or left by inputs (2 directions). The thrower was hit in 40
  pairs, on the same frame in each run of a pair: 20 thrown right, and
  20 in the hands (7 when throwing right, 13 when throwing left). In all
  102 pairs the thrower's `x`, `y` and speeds were the same on every
  frame, and so were the player's. The 20 thrown objects were the same
  as the plain barrel on every frame; the 20 in the hands were as above.
  No run had the metal barrel carried, or thrown left.
- The thrower, a second set of 225 pairs of runs of 150 to 170 frames
  (a setup method was used: the player stood on the ledge, or on the
  floor left or right of it; inputs then threw, walked or jumped, with
  and without a direction held, the jumps made by inputs), the metal barrel against a plain
  barrel with the same inputs: the thrower was hit on the same frame in
  the metal run and in its control in all 197 runs that had a hit, and
  the thrower's rows and the player's rows (`x`, `y`, speeds and
  action) were the same on every frame in all 225 pairs. The hits were
  172 of a thrown object (77 moving left, 95 moving right), 22 in the
  hands and 3 of an object carried in a jump (the player's action
  there was the carrying jump's). The thrown objects were the same as
  the plain barrel on every frame in 166 runs; in the other 6 the
  object met the wall of the ledge on the next frame, where the plain
  barrel broke and the metal barrel turned back (as under "Turning back
  at a wall"). The objects in the hands and the 3 carried were kept as
  above (`vx` 0, `vy` −5 px per frame, then falling); those in the hands
  were removed 42 to 49 frames after the hit (the first set's 39 to 45
  were from other places) and the 3 carried 47, 48 and 48; on its last
  frame the falling barrel was 249 to 256 px below the top of the
  picture. Of the 28 runs without a hit, 25 were the same as their
  control; in 3 the plain barrel broke and the metal barrel did not,
  and these were not compared further.
- Balls, arranged one frame at a time with a setup method (these
  situations are not reachable in play): 14 grids of 6 561 positions
  of a ball beside the object, offsets from −80 to 80 px in `x` and
  −75 to 85 px in `y` in steps of 2. The grids were: 7 throws to the
  right, at frames Q + 1, Q + 10, Q + 20, Q + 22, Q + 37, Q + 38 and
  Q + 51 (the one at Q + 21 was not run); 4 throws to the left, at four
  other frames from the start of the throw's hold to its flight (not
  counted from Q, so not listed); and 3 carried states: standing still
  right after the barrel was picked up, standing still 20 frames later,
  and 8 frames into a jump with the barrel carried. The ball was removed
  in 22 498 positions, the same ones as with the plain barrel in every
  grid. A thrown metal barrel was the same as the plain barrel after the
  frame in 11 138 of them (all of its speeds, `x` and `y`); the other
  11 360 were in the hands or carried, with the metal barrel left as
  above and no pieces.
- After a carried barrel was hit by a ball at its position (a setup
  method), the player was followed for 70 frames in 4 runs: standing
  still, walking right, walking left, and jumping. In each the player
  was the same as with the plain barrel on every frame.

## The second character

Measured with the second character under control (the first following
close behind), in the same arrangement and under the same conditions as
"The characters": flat floor, a metal barrel rolling right at 3.5 px per
frame, the barrel's `y` the floor's. The second character carried the
barrel left and threw it left (Action let go with Left held); it turned
back at the wall and rolled right toward the character, as in "Turning
back at a wall" (compared with a plain barrel for the second
character's throw in "The second character throwing it"). `y` and
"above" are as in "The characters". Each paragraph below says what was measured for the second
character; what it does not mention was not measured.

**On the floor, and rising.** As for the first character: a second
character standing, crouching or walking either way is not touched; the
barrel rolls through and no hurt pose was seen (standing and crouching:
`x` unchanged).
A character who is rising in a jump is not caught.

**Coming down onto it.** A second character falling in the air is caught
on a frame if D is in range (D as for the first character: the
character's `x` minus the barrel's `x` at the end of that frame) and the
character's `y` at the start of that frame, **H**, is between two
limits above the barrel's `y`. Caught frames had H from 14.11 up to
45.89 px above; not caught at 47.14 px or more above, nor at 11.48 or
less; between 11.48 and 14.11 and between 45.89 and 47.14 was not
measured. H is the character's `y` at the start of the frame, whereas
the first character's limit above is given for where the fall would end,
so the two are not directly comparable. A fall covers 6 to 8 px per frame
there, so a character who comes down with D out of range and has D in
range on a later frame of the band is caught on that later frame. The
ranges (px; not caught outside them):

| Character faces | Caught from D = | to D = | Not caught at D = | and at D = |
|---|---|---|---|---|
| left | −20.152 | 23.168 | −20.914 or less | 24.16 or more |
| right | −25.984 | 17.789 | −26.238 or less | 18.062 or more |

These are not the first character's ranges: 43.3 px wide facing left
and 43.8 facing right, against 50.1 for both. Every tried jump with D inside a range on a frame with H inside the
band was caught (98 distinct left-facing and 138 distinct right-facing
values of D, ends included; gaps between neighbouring values up to 1.18
px on the left and 0.63 px on the right), so each end lies between the
last caught value and the first not-caught one in the table, no closer.

**Riding.** On the catch frame C the character's `y` becomes the
barrel's `y` − 16.6 to 16.8 (in the 50 catches checked for this) and its `x` the barrel's `x` (at the end of C) minus a
distance between 7.8 and 8.5 px that differs from catch to catch. On
C + 1 the character's `x` does not change; from then on the barrel is
11.3 to 12.0 px ahead of it (the same distance for the whole ride in each
catch) and the character moves with the barrel, 3.5 px per frame. The
height stays between 16.0 and 17.0 px above the barrel's `y`, wobbling
by less than 1 px from frame to frame on flat floor (17.9 once the
floor began to rise, in the one arrangement that reached it in the 60
frames followed). Left, Right, Down, Up and Action held for 10 frames,
10 frames after the catch, change nothing: the ride is frame for frame
the same as without the input.

**Jumping off.** As for the first character: a Jump press that begins
on C + 2 or later starts an ordinary jump from the riding height on that
frame, with `vx` the barrel's 3.5 px per frame falling by one eighth
each frame (3.06, 2.68, 2.35 on the next frames). A press that began on
C, C − 1 or C − 2 and is still held on C + 2 jumps on C + 2 (earlier
starts were not tried); a press that began on C + 1 does nothing, however
long held (1 to 6 frames tried, and once 40).

**The ride ends** as for the first character: one ride was followed for
933 frames, from the catch to a face rising to the right; on the last of
them the barrel was 11.3 px ahead, and on the next frame the character
stood on the floor at its `x` of the last frame, no longer riding, and
walked on at its ordinary speed. The barrel was
not removed during the ride.

**A barrel still in the air.** A metal barrel in the air after the second
character's throw can also catch a falling second character who faces
the way the barrel moves. Only the following was measured (below). On
the catch frame C the character's `x` becomes the barrel's `x` (at the
end of C) minus 8.0 to 8.5 px (the barrel's `x` plus that for a barrel
moving left), and its `y` becomes 15.3 to 16.9 px above the barrel's
`y`. From C + 1 on the barrel is 11.5 to 12.0 px ahead of the character,
and from C + 2 the character moves with the barrel at 3.5 px per frame,
as in "Riding". Every catch was of a character who was falling on that
frame, with D (as above) from −17.0 to 26.0 px facing left (the barrel
moving left) and from −26.5 to 17.0 px facing right (the barrel moving
right); the grid's places were 2 px apart, so the ends of the ranges
are not known closer than that, and the ranges of the table above were
not shown to hold for a barrel in the air. A character standing on the
floor was never caught by a barrel in the air, and nothing else happened
to it (no change of pose, `x` or `y`). Catches by a barrel moving
left were found only at 14 and 18 frames after the Jump press (at 6 and
10 there were none; at 22 and 30 the barrel was outside the picture).
This was seen only in the arrangement in the evidence below, not in
ordinary play.

**Picking it up.** Action pressed with the rolling barrel 57, 53, 46, 39,
36, 29 or 18 px from the character, standing and facing it, picked it up
(walking to it first when the barrel was out of reach), as in
`carrying.md`.

### A barrel rolling left, second character

The second character under control (the first following) alone with a
metal barrel **rolling left** on the floor at 3.5 px per frame, which
had turned back at a face to the right of the character and come back
along the floor, as for the first character in "A barrel rolling left".
`y`, "above", D and H are as there and in "The second character". The
situations were arranged (see "Evidence for a barrel rolling left,
second character"). Only the following was measured; what is not
mentioned was not.

**Coming down onto it.** A second character falling in a jump straight
up is caught on the first frame, among those whose H (the character's
`y` at the start of the frame, above the barrel's `y`) is from 16.04 to
45.53 px, on which d is inside the range of the table. d is the
character's `x` rounded up to a whole pixel minus the barrel's `x`
rounded down to a whole pixel, the barrel's at the end of that frame. In
this jump the fall's frames began 51.82, 45.53, 38.81, 31.64, 24.04,
16.04 and 8.04 px above the barrel; so the frames that count are five,
and d changes by about 3.5 px from one of them to the next, the barrel
moving.

| Character faces | Caught if d is | D seen when caught, from | to |
|---|---|---|---|
| left | −16 to 27 | −17.379 | 26.570 |
| right | −22 to 21 | −23.359 | 20.984 |

Whole pixels were used because D alone does not separate the jumps: the
ends of D that were caught (and the nearest not caught) differ from jump
to jump by up to 0.3 px, and d separates them without an exception (see
the evidence). The ranges are 44 pixels wide, as for the second character
and a barrel rolling right (43.3 and 43.8 px in D) and not the first
character's 50.1 px for a barrel rolling left. Most catches (911 of
1 295 facing left, 1 161 of 1 488 facing right) came on the first of the
five frames (H 45.53); the others came on the later ones, with d in the
range on those frames only, so a character whose d was in range on a
later frame was caught on that one: the lower the frame, the
nearer the barrel. None was caught on the frame that began 51.82
px above the barrel, though d was inside the range on it in 826 left-facing and 1 153
right-facing jumps (all but 11 and 82 were caught on a later frame), and
none on the frame that began 8.04 px above it, though d was inside
the range on it, and on no frame before, in 96 left-facing and 70
right-facing jumps. So the limits of H are between 45.53 and 51.82 px
and between 8.04 and 16.04 px; where in those gaps was not measured.
The fall with a shorter or longer jump was not tried.

**Riding.** As for the second character on a barrel rolling right,
mirrored. On the catch frame C the character's `y` becomes the barrel's
`y` minus 16.04, 16.64 or 16.81 (in the 217 catches checked), and its `x`
the barrel's `x` (at the end of C) plus a distance of 7.51 to 8.96 px
facing left or 7.69 to 8.98 px facing right, which differs from catch to
catch. On C + 1 the character's `x` does not change while the barrel
moves 3.5 px to the left, so the distance becomes 3.5 px larger (11.01 to
12.46 px facing left, 11.19 to 12.48 facing right); from C + 2 the
character moves with the barrel, 3.5 px per frame to the left, at that
distance for the next 60 frames in every catch. The height above the
barrel stays between 16.00 and 17.00 px over those 60 frames. The barrel
moves at 3.5 px per frame to the left.

### Evidence for the second character

By the same setup method as for the first character, then with the
second character under control (the first following). Inputs only from
then on, after the character had picked the barrel up, carried it left
and thrown it left. That arrangement is not seen in ordinary play, so
**none of this was seen in ordinary play.**

- Catch ranges and height band: 1 536 jumps, each a standing wait of 3
  to 40 frames, then a jump straight up, after pressing Left or Right
  for 1 to 12 frames to set the facing and the place. 624 had Jump held
  for 20 frames (waits 3 to 28; 12 press lengths × 2 directions × 26
  waits) and 912 held it for 4, 8, 12 or 16 frames (waits 3 to 40, 6
  Left press lengths, 38 waits each) to put the fall at other heights.
  Facing was taken at the jump, not from the direction pressed: a Right
  press of 1 frame left the character facing left in all 26 such jumps
  (one per wait), and of 2 frames in 1 of 26. So 1 251 jumps were
  left-facing (485 caught) and 285 right-facing (179 caught). On 5 534 frames, in 699 jumps, the
  character's `y` was going up with D inside the range; none was a
  catch.
- Riding values: the 50 caught jumps of a grid of 2 directions × 5
  press lengths (1, 3, 5, 7, 9 frames) × 7 waits (8 to 20 frames in steps
  of 2), each with the distance and the height on the catch frame,
  the next two frames and the following 60 frames.
- Passing: the barrel rolling toward a second character who stood,
  crouched, walked left for 50 frames or walked right for 160 frames
  (one run each, repeated once): no hurt pose appeared and the barrel
  came within 0.85 px of the character's `x` (standing, crouching) or
  overtook it (walking right) or met it (walking left). Walking was not
  compared with the same inputs without a barrel. Two jumps straight up
  after waits of 40 and 60 frames were not caught.
- Ride: one ride of 933 frames to the face; 5 runs holding Left, Right,
  Down, Up or Action for 10 frames.
- In the air, by a setup method (as for the first character): after
  the second character's throw, a metal barrel was in the air at 80
  px behind to 80 px ahead of the character (81 places, 2 px steps), and
  the run was followed for 12 frames and compared with the character's
  run in the same inputs with no barrel near it. With the character
  standing on the floor: 2 grids of 1 458 placements (81 places times
  18 heights: level with the character and 2 to 66 px above it in 4 px
  steps), the barrel thrown right and left, the character facing the
  way it moves; no catch, no difference from the run with no barrel.
  With the character jumping straight up (Jump held for 40 frames):
  grids of 1 377 placements (81 places times 17 heights, from 48 px
  below to 16 px above the character's height on that frame, in 4 px
  steps), by the number of frames after the Jump press: facing right,
  the barrel thrown right, 6, 14, 22 and 30 frames (0, 44, 386 and 403
  catches); facing left, the barrel thrown left, 6, 10, 14 and 18
  frames (0, 0, 44 and 247 catches). Two further grids (22 and 30
  frames, facing left) had the barrel outside the picture and gave no
  data. All 1 124 catches (833 facing right, 291 facing left) were on
  a frame on which the character was falling, and the values above held
  in every one. The character facing against the barrel's direction
  was not tried. The Jump came 49 frames (facing right) or 46 frames
  (facing left) after Action was let go for the throw; a Jump pressed
  10 frames after it did not start a jump.
- Jump-off: Jump held for 1 to 6 frames starting on C − 2 to C + 4 (42
  runs). Starting on C + 2, C + 3 and C + 4, each jumped on its first
  frame; starting on C + 1 none jumped; starting on C, C − 1 and C − 2,
  held through C + 2 (3, 4 and 5 frames or more), the jump came on C + 2.
  One more run held Jump from C + 1 for 40 frames: no jump.
  The same grid with the first character (30 runs, C − 2 to C + 2, in
  its own Evidence) gave the same pattern.
- Pick-up: 7 runs.

### Evidence for a barrel rolling left, second character

By a setup method a metal barrel was made available to pick up on the
flat floor in the first level, which on screen ended about 230 px to the
right of the second character in a face rising 32 px. The second
character (leading; the first following) picked the barrel up, carried
it left to a chosen place, turned right (Right held for a chosen number
of frames while carrying) and threw it right (Action let go with Right
held). The barrel flew to the face, turned back in the air, and rolled
left along the floor past the character, who stood where it had thrown
from. That arrangement is not seen in ordinary play, so **none of this
was seen in ordinary play.** The character's inputs after the throw were
a wait of 42, 44 or 46 frames (the throw lasts about 42 frames and
presses before that were ignored), a press of Left or Right of 1 to 12
frames (to set the facing and the place), a second wait of 22 to 50
frames, and a Jump held for 20 frames.

- Catch ranges: 7 656 jumps, in 11 grids of 12 press lengths × 29 second
  waits × 2 directions (348 per direction each): the three first waits
  (carry stopped at the same place, a turn of 5 frames), and at the first
  wait of 42 frames four other places for the carry to stop (which gave
  the same `x` in some) and four other turn lengths (2, 3, 4 and 7
  frames), so that the barrel's and the character's `x` met at different
  fractions of a pixel. The facing at the jump was the direction pressed
  in every run: 3 828 left-facing (1 295 caught) and 3 828 right-facing
  (1 488 caught). The rule above held in all 7 656 (no exception, and
  the frame of each catch the one the rule gives); the frames after the
  first of the five were the catch frame in 384 left-facing and 327
  right-facing jumps.
- Riding values: 217 catches (105 facing left, 112 right), every fifth
  of the caught jumps of the three first-wait grids (527 caught facing
  left and 560 facing right in those grids), with the distance and the
  height on C, C + 1, C + 2 and the extremes over the 60 frames after.
  The catch frame C began, in these catches, with the character H above
  the barrel of 45.53 px in 164, 38.81 in 11, 31.64 in 19, 24.04 in 9
  and 16.04 in 14.

## Stingers

A metal barrel rolling right at 3.5 px per frame on a flat floor
defeats a stinger (`stinger.md`) that doesn't move at all, and goes on
as if the stinger weren't there. Contact is decided like a
stinger's contact with the player (`stinger.md`, "Touching the
player"), from whole-pixel positions at the end of a frame F, once
everything has moved:

- **I** = the barrel's `x` rounded up minus the stinger's `x` rounded
  down;
- **A** = how many pixels the stinger is above the barrel: the barrel's
  `y` rounded up minus the stinger's `y` rounded up.

The stinger is hit on frame **K** = F + 1 when I is from −28 to 32 and A
is from −28 to 19. Both ends are included. Stingers from 28 px below the
barrel (A of −28) up to A of 19 are hit when I is in range; the stinger
is not hit when A is 20 or more (its `y` 20 px or more above the
barrel's), nor when A is −29 or less (29 px or more below it). The
barrel is not slowed, lifted or removed by the hit. The knock-off below
and the barrel's rows after the hit are the same for every stinger that
is hit, including those from 28 to 21 px below the barrel (A of −28 to
−21; see the evidence below).

The stinger's knock-off, from K (it is moving only in the ways below,
and nothing of its own flight is kept):

1. On K it doesn't move, and its `vx` becomes −4 px per frame
   (−1024/256), to the left, back toward where the barrel came from.
2. On K + 1 it doesn't move. Its `vy` becomes −6 px per frame (up,
   −1536/256).
3. From K + 2, every frame: `vx` takes one step of the speed rule
   toward 2 px per frame to the left (−1024/256, −960/256 on K + 2,
   −904/256, −855/256, −813/256 and so on, as for a walker in
   `thrown-objects.md`, "The walker"), `vy` increases by 0.4375 (7/16)
   but never above 8, and it moves by both. On K + 2 it moves 3.75 px
   left and 5.5625 px up. It never lands and falls through the floor.

When the knocked-off stinger is removed was measured only in part: in 11
of the 398 hit runs it was gone on K + 2, in 309 it was there until
K + 38 to K + 43 and then gone, and in the 78 runs that stopped after 30
frames it was still there. Whether it can come back was not measured (see "Not covered").

A knocked-off stinger can still hurt a standing player. The player is
hurt on K + 1, the frame after the hit (the hit frame H of
`knock-out.md`), exactly when a stinger that was not hit but stood at the
same place would hurt the player: with I as in `stinger.md` ("Touching
the player") for a standing player facing left on the floor, from −27 to
26 (the stinger's `x` on K; it does not move on K or K + 1), at an A of 5
(A was not varied). The hit changes neither the range nor the frame (see the
evidence below). From H to H + 10 the barrel and the stinger did not
move, the freeze of `knock-out.md`; the frames after that were not
compared.

This is the walker's knock-off (`thrown-objects.md`, "The walker") with
its sideways steps starting one frame later: a walker moves sideways on
K + 1, a stinger doesn't. Its rise starts on K + 2 as a walker's does.

### Evidence for stingers

By a setup method, as in "Rolling right on the flat floor": the barrel
thrown left at the 64 px step, turned back by it, rolling right on the
flat floor at `x` of 260 or more; a stinger that doesn't move stood
ahead of it at the barrel's `x` plus an offset and at the floor's height
plus an offset, on the first frame the barrel was rolling there.

- Four grids, every run with one stinger standing ahead of the barrel
  from the first frame the barrel was rolling there:
  - 578 runs of 60 frames: heights from 20 px below to 60 px above in
    steps of 5 (17), offsets from −40 to 59 in steps of 3 (34); 248 hit.
  - 246 runs of 70 frames: heights of 17 to 22 px above in steps of 1
    (6), offsets from −100 to 60 in steps of 4 (41); 72 hit.
  - 74 runs of 30 frames at the barrel's own height, offsets from −34 to
    −20 and from 24 to 46 in steps of 0.5 (29 + 45); 70 hit.
  - 9 runs of 30 frames at heights of 19 to 20 px above in steps of
    0.125, one offset; 8 hit (19 to 19.875 above hit, 20 did not).
- Three more grids, for stingers well below the barrel (same setup):
  - 176 runs of 41 frames each (6 311 frames checked): heights from 60 to 25 px below in steps of 5
    (8), offsets from −40 to 44 in steps of 4 (22); 19 hit, all at 25 px
    below.
  - 407 runs of 41 frames each (3 761 frames checked): heights from 30 to 20 px below in steps of 1 (11), offsets
    from −32 to 40 in steps of 2 (37); 324 hit (every height from 28 px
    below up, none at 29 or 30).
  - 134 runs of 41 frames each (2 758 frames checked): heights of 28 and 29 px below, offsets from −30 to 36 in
    steps of 1 (67); 67 hit, all at 28 px below.
  In all 717 runs the stinger was hit on frame F + 1 for the first F
  whose I and A were inside the ranges above (A from −28), and on no
  other frame (no mismatch; 12 830 frames checked in all, counted as in the next
  bullet).
  The I seen on hit frames ran from −28 to 31 (none at 32 was seen at
  these heights). The hits' knock-off and the barrel's rows were not
  followed beyond the hit frame in these runs.
- After-effects of hits at 28 to 21 px below (a further 120 runs of
  60 frames: heights of 28 to 21 px below in steps of 1 (8), offsets
  from −24 to 32 in steps of 4 (15); every one was hit): the knock-off
  had the steps above frame for frame (speeds, and positions relative
  to K) in all 120, over the 4 515 frames from K until the stinger was
  removed. That was on K + 37 in 45 runs and on K + 38 in 75 (none on
  K + 2), so the removal seen in all runs so far came on K + 2 or from
  K + 37 to K + 43. The barrel's rows and the player's were the same as
  in two runs with the stinger 60 px below (never hit) in all 120, and
  the player was not hurt in any.
- In all 907 runs the stinger was hit on frame F + 1 for the first F
  whose I and A were inside the ranges above, and on no other frame
  (no mismatch). The frames checked were those from the first frame the
  stinger stood there up to the hit, or to the end of the run if
  it wasn't hit: 20 472, 12 381, 293 and 38 in the four grids, 33 184 in
  all (the frames after a hit, and the first, which could not be a hit,
  are not counted).
- Every one of the 398 hit runs (248 + 72 + 70 + 8) had the knock-off
  above frame for frame (speeds, and positions relative to K) as far as
  it was followed: 309 for 38 frames or more, the others until the end of
  the run (30 frames) or the stinger's removal on K + 2 (11).
- A knocked-off stinger and a standing player: the barrel rolled right
  at 3.5 px per frame and the stinger stood 20 px ahead of it (hit on K
  every time), the player on the floor facing left, 5 px below the
  stinger (A = 5), at I = −44 to 44
  from the stinger's `x` (51 runs: every even I, and −29, −27, −25, 25,
  27, 29). The player was hurt on K + 1 in the 30 runs with I from −27
  to 26 and in none of the 21 others. The same I with the stinger 100 px
  ahead of the barrel, so not hit (12 runs: −34, −30, −28, −27, −26, 24
  to 28, 30, 34): hurt on the second frame counting from the first with
  the stinger there (the hit runs: one frame after the hit, which came on
  the first) in the 5 runs with I from −27 to 26, and not in the other 7. In all
  30 hurt hit runs the barrel and the stinger did not move from H to
  H + 10.
- The barrel's and the player's rows were the same with and without a
  hit (41 pairs of runs with the stinger 19 and 20 px above, 24 of them
  hits).

### A stinger that sways

A stinger that flies (`stinger.md`, "Moving") is hit by a metal barrel
rolling right at 3.5 px per frame on a flat floor by the same ranges as
one that doesn't move: I from −28 to 32 and A from −28 to 19, both ends
included. The two positions are taken from different frames, as the
stinger moves on the hit frame itself:

- the barrel's `x` and `y` at the end of the frame before the hit frame K;
- the stinger's `x` and `y` at the end of K, after its own move on K.

I and A are formed from them as above (the barrel's `x` rounded up and
the stinger's rounded down; `y` rounded up for both). For a stinger that
doesn't move the two frames give the same stinger position, so this is
the rule above with F = K − 1.

The knock-off is the one above with a stinger's own flight added on K:
on K the stinger makes its own move of that frame (and no other),
and its `vx` becomes −4 px per frame; on K + 1 it doesn't move and its
`vy` becomes −6 px per frame; from K + 2 it is the same flight, whatever
the stinger's sway was (none of it is kept). Positions relative to its
position on K were the same in every run, from K + 1 as far as followed.

#### Evidence for a stinger that sways

- 1 638 runs: the stinger's start at 40 px behind to 90 px ahead of the
  barrel's `x` on the first frame followed (every 10 px, 14 values),
  30 px below to 50 px above the floor (every 10 px, 9 values), for 13
  of the 14 settings of `stinger.md` (all but the one that doesn't move).
  The first frame followed is the stinger's first, at its start; the barrel
  was rolling right at 3.5 px per frame on the flat floor at `x` of 261
  (the same in every run). A run ends 60 frames later or when the player is hurt.
- The stinger was hit on the frame the rule gives, and on no other, in
  all of 34 229 frames checked. A run is checked from its second frame
  (the first after the stinger's start, which can already be a hit: 312
  runs were hit on it) up to its end, which was the first of: the hit
  (871 runs), the player being hurt (111 runs, before any hit), the
  stinger being gone (120 runs) and the barrel being removed
  after it rolled out past the right of the picture (536 runs, all on
  the 44th frame after the first frame followed, counting that one as
  the 0th: the barrel was last seen at `x` 411.5 and the picture had not
  scrolled). I seen in the hits: −28 to 32; A:
  −28 to 19. The barrel rolled right at 3.5 px per frame on every frame
  checked. Frames after the player was hurt are not checked: the picture
  stood still from then (the barrel's `x` did not change). Frames after the barrel was removed are not checked
  either: no barrel was there. Taking the stinger's position at the end of the
  frame before K, as for a stinger that doesn't move, gave many wrong
  frames.
- In all 871 hit runs the stinger did not move from K to K + 1 and
  its rows from K + 1 were the same relative to K: 754 followed for 30
  frames, 117 gone on K + 2.

### Rolling left

A metal barrel rolling left on a flat floor, 29 to 42 frames after it
left the hands and moving left at 3.56 to 3.77 px per frame (measured
over those frames), defeats a stinger that doesn't move at all in the
same way, and goes on as if the stinger weren't there. Barrels of other
ages or speeds were not tried. Contact is decided from whole-pixel positions at the end
of a frame F, as for rolling right, with the barrel's `x` rounded to the
nearest whole pixel (halves up) instead of up:

- **I** = the barrel's `x` rounded to the nearest minus the stinger's
  `x` rounded down;
- **A** = the barrel's `y` rounded up minus the stinger's `y` rounded up,
  as above.

The stinger is hit on frame **K** = F + 1 when I is from −25 to 34 and A
is from −28 to 19. Both ends are included; the stinger is not hit when A
is 20 or more, nor when A is −29 or less (29 px or more below the
barrel, as for rolling right). Stingers from 60 px below the barrel up
to A of 19 were tried. The knock-off and the barrel's rows after the
hit are the same for every stinger that is hit, including those from 28
to 21 px below (A of −28 to −21; see the evidence below). The barrel is
not slowed, lifted or removed by the hit. The
knock-off is the one above mirrored: on K the stinger doesn't move and
its `vx` becomes +4 px per frame (to the right, back toward where the
barrel came from), on K + 1 it doesn't move and its `vy` becomes −6 px
per frame, and from K + 2 it follows the same steps toward 2 px per
frame to the right, moving 3.75 px right and 5.5625 px up on K + 2.

When the knocked-off stinger is removed was measured only in part: of
the 288 hit runs of the first grid below, all followed to the end of
their 60 frames, the stinger was gone on K + 2 in 184 and was there
until K + 38 to K + 43 and then gone in 104.

#### Evidence for rolling left

By a setup method: the barrel thrown left from `x` 540 on the flat floor
while the player keeps walking left, so that it rolls left on the floor
at `x` of 340 or less; a stinger that doesn't move stood at the
barrel's `x` plus an offset and at the floor's height plus an offset on
the first frame the barrel was rolling there.

- Four grids, 786 runs of 60 frames, 353 of them hits:
  - 697 runs: heights from 20 px below to 60 px above in steps of 5
    (17), offsets from −80 to 40 in steps of 3 (41); 288 hit.
  - 44 runs at the barrel's own height, offsets from −75 to −60 and from
    22 to 28 in steps of 0.5; 40 hit.
  - 28 runs at the barrel's own height, offsets from −27 to −21 and from
    31 to 38 in steps of 0.5; 13 hit.
  - 17 runs at heights of 18.5 to 20.5 px above in steps of 0.125, one
    offset; 12 hit (up to 19.875 above hit, 20 did not).
- In all 786 runs the stinger was hit on frame F + 1 for the first F
  whose I and A were inside the ranges above, and on no other frame (no
  mismatch in 27 511 frames checked: from the first frame the stinger
  stood there up to the hit, or to the end of the run). Frames with I of
  −26 and −27 (12 frames) and of 35 and 36 (81 frames) at A of 19 or
  less were checked and are not hits. The frames after a hit are not
  counted.
- Every one of the 288 hit runs of the first grid had the knock-off
  above frame for frame (speeds, and positions relative to K), as far as
  it was followed (38 frames or more in the 104 runs where the stinger
  stayed until K + 38 to K + 43; in the other 184 until its removal on
  K + 2).
- The barrel's and the player's rows were the same with and without a
  hit in 36 pairs of runs (the stinger 15 and 20 px above, one hit and
  one not), for all 61 rows in 27; in the other 9 the player's rows were
  the same until the player, still walking left, reached the stinger
  that had not been hit, 48 to 60 frames in, where the player's action
  changed. The barrel's rows were the same to the end in one of those 9
  and until one frame after the player's change in the other 8.
- The barrel's `vx` on the frames up to each hit ran from −966/256 to
  −911/256 px per frame (−3.77 to −3.56), 29 to 42 frames after it left
  the hands; at the first frame followed it was −3.77 in all 786 runs.
- Two grids for stingers well below the barrel (same setup, 61 frames):
  - 168 runs: heights from 60 to 25 px below in steps of 5 (8), offsets
    from −44 to 36 in steps of 4 (21); 18 hit, all at 25 px below.
  - 374 runs: heights from 30 to 20 px below in steps of 1 (11), offsets
    from −36 to 30 in steps of 2 (34); 288 hit, at every height from 28
    px below up and none at 29 or 30 px below.
  In all 542 runs (306 hits) the stinger was hit on frame F + 1 for the
  first F whose I and A were inside the ranges above (A from −28), and
  on no other frame: no mismatch in 13 939 frames checked (from the
  second frame the stinger stood there up to the hit, or to the end of
  the run; the frames after a hit are not counted). 1 676 of those
  frames had A of −29 or less with I in range, and none was a hit. The I seen on
  the frames before the hits ran from −25 to 33. The hits' knock-off and
  the barrel's rows were not followed beyond the hit frame in these
  runs.
  Nine of the 374 runs (all at the offset −34, one per height from 28 to
  20 px below; their hits are among the 288) had I of 35 on the first
  frame the stinger stood there, and the stinger was hit on the next
  frame. The rule above does not cover that case (I of 35 on the first
  frame), and the no-mismatch claim holds only from the second frame on;
  it is listed in "Not covered". I of 35 on a later frame was not a hit
  in any run.
- After-effects of hits at 28 to 21 px below (a further 136 runs of
  61 frames, same setup: heights of 28 to 21 px below in steps of 1 (8),
  offsets from −40 to 24 in steps of 4 (17); every one was hit): the
  knock-off was the one above mirrored, frame for frame, in all 136, over
  the 2 837 frames from K until the stinger was removed. It was removed
  on K + 2 in 64 runs, on K + 37 in 27 and on K + 38 in 45. The barrel's
  rows and the player's were the same as in two runs with the stinger
  60 px below (never hit) in all 136, and the player was not hurt in
  any.

### Rolling left, a stinger that sways

A stinger that flies (`stinger.md`, "Moving") is hit by a metal barrel
rolling left on a flat floor (as in "Rolling left" above: 29 to 42
frames after it left the hands, at 3.5 to 3.77 px per frame) with the
two frames of "A stinger that sways" and the roundings of "Rolling
left":

- the barrel's `x` (rounded to the nearest whole pixel, halves up) and
  `y` (rounded up) at the end of the frame before the hit frame K;
- the stinger's `x` (rounded down) and `y` (rounded up) at the end of K,
  after its own move on K.

The stinger is hit on K when I is from −26 to 34 and A is from −28 to
19, both ends included. A has the same ends as for a stinger that
doesn't move; I begins one pixel lower (−26, where a stinger that
doesn't move is not hit at −26).

The knock-off is the one for a stinger that doesn't move, rolling left
(on K the stinger makes its own move of that frame and its `vx` becomes
+4 px per frame, to the right; on K + 1 it doesn't move and its `vy`
becomes −6 px per frame; from K + 2 the same flight, 3.75 px right and
5.5625 px up on K + 2), whatever the stinger's sway was.

The stinger was gone on K + 2 in 3 222 of the 4 307 hit runs below, and
on K + 37 to K + 43 in 1 008 (K + 37: 147, K + 38, K + 39, K + 41 and
K + 42: 104 each, K + 43: 445); the other 77 ended first. What decides
which was not found.

#### Evidence for rolling left, a stinger that sways

By a setup method: the barrel thrown left from `x` 540 on the flat
floor while the player keeps walking left, as for rolling left. The
first frame followed is the stinger's first, with the stinger at the
barrel's `x` plus an offset and at the floor's height plus an offset. On
that frame the barrel was at `x` 336.5, moving left at 3.77 px per frame
(slowing to 3.5 px per frame within 23 frames), in every run. A run
ends 60 frames later or when the player is hurt. The 13 settings of
`stinger.md` other than the one that doesn't move were used.

- 6 561 runs, 4 307 of them hits:
  - 2 925 runs: offsets from −80 to 40 in steps of 5 (25), heights from
    30 px below to 50 px above in steps of 10 (9), 13 settings.
  - 3 042 runs: offsets from −40 to −15 in steps of 1 (26), the same
    heights, 13 settings.
  - 594 runs for the edges of A: offsets from −20 to 20 in steps of 5
    (9), heights from 30 to 26 px below and from 17 to 22 px above in
    steps of 1 (11), 6 settings.
- The stinger was hit on the frame the rule gives, and on no other, in
  152 929 of the 152 981 frames checked, counting from each run's
  second frame and stopping at the hit, or before the frame the player
  was hurt. The 52 frames the rule did not give were all the second
  frame of a run, at I of 35, with the start 34 px ahead of the barrel (to its left, in its path) and
  at 20 px below to 10 px above, 13 settings: the stinger was hit. They
  are left out of the rule above and listed in "Not covered". Of the
  6 561 runs, 4 307 ended in a hit, 420 with the player hurt and no hit
  (on the 16th to the 61st frame followed), and 1 834 went the 60 frames
  with neither. The barrel was still there in every one of them up to
  the frame the player was hurt; frames after that are not checked. Seen
  in the hits: I from −26 to 34; at I of −27, 11 frames were not hits,
  and at 35, 591 were not (besides the 52 above). At A of −28, 186
  frames were hits and at −29, 261 were not; at 19, 807 were hits and at
  20, 1 603 were not.
- In all 4 307 hit runs the stinger did not move from K to K + 1 and its
  rows from K + 1 were the same relative to K, as far as followed
  (30 rows, or until it was gone).
- The same setup with a stinger that doesn't move (729 runs, offsets
  from −40 to 40 in steps of 1, heights from 30 px below to 50 px above
  in steps of 10) gave I from −25 to 34, as in "Rolling left".

### Rolling left, thrown by the second character

A metal barrel thrown left by the second character (it leaves the hands
on Q + 18 and first moves on Q + 19, as above) and rolling left on the
flat floor on Q + 39 to Q + 53 (20 to 34 frames after it first moved),
moving left at 3.65 to 4.38 px per frame (934/256 to 1121/256),
defeats a stinger that doesn't move at all in the same way as the barrel
thrown by the first character does in the section above, and goes on as
if the stinger weren't there. I and A are as in "Rolling left" and the
stinger is hit on frame **K** = F + 1:

- A from −28 to 19 (28 px below the barrel up to 19 px above it; the
  stinger is not hit when A is 20 or more, and was not hit 30 px below;
  where between −30 and −28 the lower end lies was not measured, the
  tried heights below the barrel being −30 and −28 to −10 in steps of 2);
- I from −25 to 34: the stinger is hit on the first such frame;
- I of −26 or 35: the stinger is sometimes hit, sometimes not (see
  "Evidence"), and I of −27 to −29 and of 36 and 37 was never a hit. So the
  ends are about one pixel wider than for the first character's barrel
  at 3.56 to 3.77 px per frame; what decides at I of −26 and 35 was not
  found.

The first three frames of the knock-off are those of "Rolling left": on K the stinger doesn't
move and its `vx` becomes +4 px per frame (1024/256), on K + 1 it doesn't
move and its `vy` becomes −6 px per frame (1536/256, up), and on K + 2
it moves 3.75 px right and 5.5625 px up with `vx` 3.75 px per frame
(960/256) and `vy` −5.5625 px per frame (1424/256). Only K, K + 1 and
K + 2 were compared with that section, and so were the later frames
of the knock-off for a barrel at its ordinary speed, up to the 38 frames
from K that section gave (the same steps, frame for frame; see the
evidence below). The barrel is not slowed or lifted by the hit.

The stinger is removed on K + 2 when its `x` rounded down minus the
view's position (as in `walker-view.md`, "Defeated walkers falling out")
is below −31 after that frame's move. With the view's position near 196
that was every stinger that stood 10 px or more to the left of the
barrel's `x` (I of 10 or more) and none that stood 9 px or less to the
left or anywhere to the right of it. Otherwise it was seen to be removed
while it falls through the floor, on the frame it has fallen far enough:
its height was still 95 px or less below the
floor's on the frame before in all the runs and 97 px or more below on
the frame it was gone. That was on K + 37 for a stinger 28 px below
the barrel's height, K + 39 for 10 px below, K + 41 at the barrel's
height, K + 42 for 10 px above and K + 43 for 19 px above. Whether
this is the vertical edge of the walker rule was not checked (the
picture's vertical position was not recorded).

#### Evidence for the second character's barrel against a stinger

By a setup method: the second character picks the barrel up, walks to
`x` 380, turns left and throws it left; a stinger that doesn't move
stood at the barrel's `x` plus an offset and at the floor's height plus
an offset on the first frame the barrel was on the floor moving left with
its `x` at or below a limit.

- 1 902 runs of 41 frames (duplicates removed), in six grids; "limit" is
  the `x` the barrel had to be at or below, "height" is the stinger's
  height above the floor in px (negative: below), "offsets" are the
  stinger's `x` minus the barrel's, in px:
  - limit 170; heights 10, 17, 18, 19, 20, 21, 22, 25, 30, 40; offsets
    −70 to 30 in steps of 2 (51): 510 runs;
  - limit 170; height 0 (the barrel's own); offsets −70 to 32 in steps of
    1 or 2 (62): 62 runs;
  - limits 120 and 150; heights 0, 19, 20; offsets −70 to 40 in steps of
    1 (111): 666 runs;
  - limit 170; height −30; offsets −70 to 40 in steps of 2 (56): 56 runs;
  - limit 170; heights −28 to −10 in steps of 2; offsets −40 to 40 in
    steps of 2 (41): 410 runs;
  - the edges of I: limits 130 to 166 in steps of 4 except 150; height 0;
    offsets −40 to −30 and 22 to 32 in steps of 1 (22): 198 runs.
  In 1 039 of them the stinger was hit while the barrel moved left (and
  its speed on the frame before was inside the window of the rule: none
  was outside); 211 other runs had the first hit when the barrel had
  turned back at the wall or on its turn, and are not described here or
  counted below.
- Frames before a hit (or in the whole run if there was none) with the
  barrel at the speeds above: 11 019 over all runs. Of those, with A of
  −22 to 19, I of −25 to 34 was a hit on the next frame in
  every case (14 frames at −25, 25 at −24, 57 at 33, 101 at 34, and
  all the ones between); I of −27 to −29 never (none of 53 frames) and
  36 and 37 never (none of 142). At I of −26, 18 of 25
  frames were hits; at 35, 24 of 49.
- In all 1 039 hit runs with the barrel moving left the stinger had the
  rows above on K, K + 1 and K + 2 (the three compared).
- After-effects at the barrel's ordinary speed (limit 170 or 130 as
  above; 139 runs of 60 frames, a stinger that doesn't move stood at the
  floor's height plus a height and at the barrel's `x` plus an offset
  on the first frame the barrel was there): heights −28, −10, 0, 10 and
  19, offsets −40 to 40 in steps of 4 (21): 105 runs; heights 0 and 19,
  offsets −13 to −5 in steps of 1 (9): 18 runs; limit 130, height 0,
  offsets −30 to 0 in steps of 2 (16): 16 runs. Every run had a hit. In
  119 of them the barrel was moving left on the hit frame and the
  knock-off was the same in all: the three frames above and then the
  steps of "Rolling left", frame for frame (speeds, and positions
  relative to K), for the 38 frames from K the longest run of that
  section gave, or to the removal if sooner. The 2 286 frames from K
  until the stinger was removed (43 at the most) were followed in all;
  those after the 38th (K + 38 to K + 43, in the runs removed on K + 39
  and later) were not compared with that section. The stinger was removed
  on K + 2 in 66 and on K + 37 to K + 43 in 53 (9 at K + 37, 9 at
  K + 39, 13 at K + 41, 9 at K + 42, 13 at K + 43; the heights above in
  that order). On K + 2 the first rule above was met in all 66, and in
  the 53 others not on any frame before the removal. The other 20 runs
  (offsets 28 to 40 of the first grid) were first hit after the barrel
  had turned back at the wall, with the stinger knocked the other way and
  removed on K + 13 to K + 18; they are not described here.
- The barrel's and the player's rows (60 rows) were the same as in runs
  with the stinger 60 px below and 40 px above (never hit) at the same
  offsets. Of the 103 hit runs of the first two grids that had controls,
  85 (the first grid's, without its 20 hit after the turn) had both, 170
  pairs, and the 18 of the second grid had the one 60 px below, 18 pairs:
  188 pairs in all. The player was not hurt in any of the compared runs.
- Frames at A of 20 to 40 above, within the I range, were never hits
  (425 frames at each of 21, 22, 25, 30 and 40, from the first grid
  only; 1 145 at 20, which is also in the third grid: 720 more); at 30 px
  below, never (425 frames); at 28 to 18 px below (every 2 px), always
  (33 frames each).
- The barrel's and the player's rows were the same, for all 41 rows,
  with a hit as with the stinger 20 or 40 px above (never hit) at the
  same limit, in all 1 052 hit runs of the grids with limits of 120, 150
  and 170 (the 198 edge runs, all hit, at the other limits, were not
  compared this way; the 1 052 include runs hit after the barrel's turn).

## Critters

A metal barrel rolling right at 3.5 px per frame on a flat floor passes
a critter (`critter.md`) as if it weren't there, as `critter.md` says
for a thrown barrel: neither of them is changed by the other. In the
60 frames followed, the barrel keeps its `x`, `y`, speeds and its
removal as in "Like a barrel", and the critter's perching, flying and
fluttering go on as they do with no barrel in the picture. This was
seen for a critter sitting still and for one flying, at the heights and
distances in the ranges below, including the frames on which they
overlap. It was seen only in arranged situations (see "Not covered").

### Evidence for critters

The barrel was thrown left at the 64 px step, turned back by it and
seen rolling right on the flat floor at `x` of 260 or more (as in
"Rolling right on the flat floor"). A critter was seen at the barrel's
`x` plus an offset and at the floor's height plus an offset from the
first frame the barrel was rolling there, and the barrel was followed
for 60 frames. The first character stood about 100 px behind the
barrel.

- A critter sitting still (perched): offsets from 20 to 110 px ahead in
  steps of 6 (16), heights from 0 to 64 px above the floor in steps of
  8 (9); 144 runs.
- A critter flying down towards a point 16 px below where it started,
  and one flying up towards a point 16 px above: offsets as above,
  heights from 16 to 64 px above the floor in steps of 8 (7); 112 runs
  each.
- Each of these 368 runs was compared with two runs from the same
  frame. In the first the critter was far above the barrel's path (about
  250 px higher, nowhere near it); in the second the critter was where
  it was in the run but no barrel was in the picture. In all 368 the
  barrel's rows and the first character's rows equal those of the first,
  and the critter's rows equal those of the second. The barrel passed
  the critter's `x` in all of them.

## Pictures

What a metal barrel looks like in `title-1`. Each picture is named by its
picture hash (`../formats/sprites.md`, "Picture hashes") and drawn at the
object's position as that file says; the picture given for a frame is the
one on screen with that frame's view. These are not the barrel's
pictures (`thrown-objects.md`, "Pictures"): the metal barrel has its own.

| Picture | Picture hash (SHA-256) |
|---|---|
| `metal-lying` | `9c43f4706ed498044b71edffa8da2da48d5a2d222a040095f61eba2f0b11afaa` |
| `metal-thrown-1` | `634434fd17cc5de33861a590f3ef9d62bb69512754abc917c213b3164fc9667b` |
| `metal-thrown-2` | `363bb924b954f93f4a74a2d3314713e9ed13f82038ede805af2c4d6db517f592` |
| `metal-thrown-3` | `3c218ed7645a3ba08c06ce889c4499a2bb6c62ffbee13f425a3498219fe0b389` |
| `metal-thrown-4` | `da92b2ad468de5fa8271fc41e544e4d685b07145255b7cebada3dab1527e4768` |
| `metal-thrown-5` | `5396967b9f3d1ee3a9644ce23278d24fef05de82ebee3297639a40233ea01db0` |
| `metal-thrown-6` | `5bca3b2a2b2ac6012e4e3cc80a8c929361b9b8bf81d38beb995058aac4a72ab9` |
| `metal-thrown-7` | `6f402c16846dacb6165c832a190fbb73fdc9d874d7e8155f628586555b560157` |
| `metal-thrown-8` | `e627fdb2e8fb95999c946f2a9d2c9e54c78e5b080ea116c4db8cbd1932647c84` |

Their colours are the animal's palette (`../formats/sprites.md`, "The other
objects' palettes").

**When a hash isn't found.** The eight flight pictures belong together:
if any of them isn't found, the engine shows its own art for a thrown
metal barrel, with the timing and mirroring given here. If `metal-lying`
isn't found, the engine shows its own art for a metal barrel lying on
the floor, not mirrored.

- **Lying.** A metal barrel standing on the floor, not picked up, shows
  `metal-lying`, not mirrored. After a put-down it shows `metal-lying`,
  not mirrored, for as long as it lies there. (Its picture on the
  frames just after a put-down was behind the player; see Not covered.)
- **In flight.** From the frame it leaves the hands (Q + 21) until it is
  gone, it cycles through `metal-thrown-1` to `metal-thrown-8` exactly as
  the barrel does (`thrown-objects.md`, "In flight"): counting `j` from 0
  on Q + 21, the picture is `metal-thrown-`*n* with *n* = 1 +
  ⌊(`j` + 1) ÷ 3⌋ mod 8. The cycle goes on through bounces, rolling and
  turning back at a wall without restarting.
- **Mirroring.** Mirrored left–right exactly when the object is moving
  left, not mirrored when moving right. On the frame it turns back at a
  wall (`vx` changes sign) the mirroring changes with it, and the cycle
  goes on.
- **No flash and no pieces.** It doesn't break, so the barrel's flash or
  piece pictures don't appear when it meets a wall; it flies on with the
  cycle unbroken.

### Evidence for the pictures

By a setup method a metal barrel was made available to pick up on flat
floor in the first level, and the first character picked it up, carried
it for 0 to 90 frames walking, standing or running, and threw or put it
down at random, in either direction, in 40 runs of up to 600 frames
(12 292 logged frames). The 40 runs held 48 pick-ups: 40 throws and 8
put-downs. The picture of each frame was read from the screen with that
frame's view.

- **Flight.** There were 3 673 frames in flight. On 1 900 the barrel was
  off the screen, on 125 no picture could be read at its position, and
  on 26 it was hidden behind another picture; those 2 051 frames were
  not checked against the pictures. On the other 1 622 frames the
  picture could be read: 1 620 showed the listed picture at the listed
  mirroring, and 2 showed another picture (both with other objects at
  the same place; not explained).
- **Turns.** There were 19 wall meetings, all while moving left and
  turning to move right. The barrel kept flying after every one and
  the cycle went on. On 4 of them the picture could be read on the first
  frame after the turn, and it was already unmirrored and was the
  listed picture with no other picture of the barrel; on the other 15
  the barrel was off the screen or not readable on that frame, so the
  exact frame of the mirroring change and the absence of a flash were
  not seen there. No turn from moving right to moving left was seen.
- **Lying.** `metal-lying` was seen unmirrored on all frames where it
  could be read, standing and after the 8 put-downs. After a put-down
  the barrel was behind the player's picture on the first frames: it
  could not be read on any of the first 7 frames after the player was
  free; in 5 of the 8 it first showed `metal-lying` 14 to 61 frames
  after, and in the other 3 it was not read before it was picked up
  again.

## Not covered

- Ordinary play: the rules above were seen only in arranged situations
  (see "Evidence").
- Objects with their lowest point under the floor's top at a wall:
  an object inside the ground is lifted on a normal frame, and what
  happens on a wall frame was not measured.
- The ride's pictures and sounds, and a ride that meets a wall other
  than the one measured: not measured. For a barrel rolling left the end
  of a ride was not measured: in the one run followed, the barrel turned
  back at two faces 32 px high and the character went on riding, but
  the distance between them changed at each turn and the run ended with
  both falling through the ground, so nothing is claimed. Riding
  the barrel with both characters on the way, or the second character
  with the first leading, was not measured.
- Stingers: ones that sway, other than with the barrel rolling on the
  flat floor, right at 3.5 px per frame or left at 3.5 to 3.77 px per
  frame (not covered: a barrel in the air or on a slope, just after a
  turn, or thrown by the second character, meeting a stinger that
  sways, and a player hurt by a swaying stinger after the hit),
  a stinger below a barrel rolling left that first stands in range with I of 35 (hit one
  frame later in 9 runs, and, for a stinger that sways, hit on the
  second frame of a run in all 52 runs with the start 34 px ahead of the
  barrel), a barrel in the
  air or on a slope meeting a stinger that flies, a barrel just turned
  back at a wall, what decides the frame
  a knocked-off stinger is removed (K + 2, or K + 37 to K + 43), whether
  it can come back, and whether it can hurt a player in poses other than
  standing on the floor facing left, and at an A other than 5.
- Stingers and the barrel thrown by the second character: where between
  28 and 30 px below the barrel the lowest hit lies, what decides whether
  a stinger at I of −26 or 35 is hit (18 of 25 and 24 of 49 frames
  were), the knock-off frames after the 38th from K and the vertical edge for
  the stinger's removal, the knock-off for the barrel just after it lands (at over 4.4
  px per frame), the bottom edge as a position, whether the player
  can be hurt by a stinger standing where this barrel hits it (it
  was in 16 runs outside the three grids above, with the barrel just
  after it landed, and those were not compared), and the barrel meeting one
  other than rolling left on the
  flat floor at the speeds above (the first frames after it lands, at
  over 4.4 px per frame, were seen with a wider range, I from −25 to 61
  at 5.4 px per frame in a few runs, not measured; rolling right after a
  turn; flying or swaying stingers).
- A walker knocked off by a barrel rolling right meeting the player: in
  4 runs it hurt the player on K + 1 and the game stood still; the
  player's frames after a hit were compared with nothing.
- Critters beyond the 60 frames followed (a later perch, flight or
  leaving), and critters that came into the picture and flew on their
  own: every critter here was set up at the offsets and heights above,
  some of them places it wouldn't normally have.
- Critters other than for a barrel rolling right on the flat floor
  thrown by the first character (a barrel in the air, rolling left,
  on a slope, just after a turn, thrown by the second character, or
  a critter that flies in from the top of the picture on its own).
- The other enemies (not
  walkers, the thrower, balls, stingers and critters), and whether it defeats them or turns
  back from them. The balls for the second character other than those
  in the grids above (balls as the thrower throws them in ordinary play
  while the second character stands or walks by, a ball that was there
  from the start of the level, the ranges as positions), the thrower
  for the second character other than the ledge arrangement above (no barrel that had
  left the hands reached it there),
  and a metal barrel rolling on the floor or just turned back at a wall meeting
  them. Walkers hit by a metal barrel thrown from other heights or in
  the air, from other floors, rolling on a slope, or on the
  frames just after it turned back at a wall were not measured (the
  rolling hits above were at `x` of 200 or more, well after the turn, or
  rolling left at `x` of 340 down to 124);
  nor were the knock-off frames after the first for a barrel thrown
  by the second character beyond the 16 runs followed, or where a
  walker hit by a barrel rolling left has its heading turned on the next
  frame. For a barrel rolling left: a large walker facing right that appears with the difference
  first in the range at its upper end (26) was hit later than the first
  frame in the range, at a difference of 21 or 22 (4 of 486 runs, within
  five frames of the walker appearing), so which frame a walker that
  appears at the edge of the range is hit on is not covered, nor are
  walkers close to the step or just after turning back. A barrel thrown by
  the second character rolling right against walkers was measured only
  for the lower ends of the ranges and, once it had landed and was
  rolling on the flat floor, for its first 29 frames (the last on the
  slope), at 3.5 to 3.62 px per frame on the flat floor (see "Rolling
  right, thrown by the second character"); the upper ends, a longer
  roll, and walkers close to the hands were not. Nor is one thrown by the
  second character rolling left against a walker ahead
  of it at the range's upper ends (a difference of 17 or more), against
  one that comes into the range from outside it (every hit measured was
  of one already in range on the first frame followed), or when the barrel
  moves faster than 3.74 px per frame, nor are the walker's frames after
  such a hit.
- Q + 21 (neither in the hands nor thrown by the frames measured): not
  measured.
- The thrower with a carried metal barrel other than in a jump (for
  example carried while walking or standing), and with one thrown left
  after Q + 21 other than the runs above: not measured.
- The characters: a barrel rolling left meeting the second character
  other than coming down onto it in a jump straight up held for 20
  frames from a standstill (rising, standing, crouching, walking,
  picking it up, jumping off the ride, inputs during the ride, the end
  of the ride, the first character following close behind, the other
  vertical limits of H), or meeting a character who was moving sideways
  faster than 0.94 px per frame at the jump; the second character
  throwing it so that it rolls
  left toward the first; a barrel in the air meeting a character
  who faces against its direction (either character);
  whether a rolling barrel hurts the second character in other situations;
  what a barrel in the air does to a jumping second character it doesn't
  catch (the rising grids, and the places in the falling grids with no
  catch: not examined beyond "no catch"); the vertical limit of a catch
  in the air, and the ranges of D there (for either character); a barrel
  in the air in ordinary play (seen only in the arrangement in the
  evidence); the
  landing of a barrel with a rider on the way (only 2 rides followed);
  catching a character who comes down while moving sideways (rolling
  left: only the speeds of up to 0.94 px per frame at the jump above
  were tried); where between 17.5 and 24.8 px above the barrel
  the catch height limit lies.
- The second character throwing it other than the throws described
  above (whether the metal barrel's turn at the step differs between
  being in the air and rolling, which was not recorded, walls other than
  the step, throws in the air, hits on walkers other
  than those described, the metal barrel in the hands after a hit and
  what it does later, slopes for a second-character throw); a throw by either character with the
  metal barrel inside a wall at the release (seen twice: no break, `vx`
  0 and the object moving up at 5 px per frame; not measured).
- The pictures while it is picked up and carried and on the first
  frames after a put-down (none could be read on the screen behind the
  player), so when `metal-lying` first shows after a put-down; its
  mirroring when it isn't moving sideways in flight (`vx` 0, for
  example released inside a wall); a turn from moving right to moving
  left; the pictures of a ride, and what sounds play. Pictures were read for the first character's throws only.
