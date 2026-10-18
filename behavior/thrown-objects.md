# Thrown objects: bouncing, rolling and breaking

Titles: `title-1`

What a thrown object that holds nothing does from the moment it is
thrown until it is gone: how it bounces on the floor, how it then rolls,
how it breaks against a wall, what happens when it hits a walker, the
thrower or one of the thrower's balls, and when it is removed after leaving the picture. `carrying.md` covers the throw itself and the flight up to the
first touch; this file carries on from there. An object that holds the
missing character moves the same way until it breaks; when it breaks is
in `holder-breaking.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`,
`vx` and `vy` are as in a trace: right and down are positive, speeds in
px per frame. `y` is a level y as in `view-vertical.md`. Speeds are
whole multiples of 1/256 px per frame and are written both ways, as in
`ground-speed.md`. **The speed rule** is the rule of `ground-speed.md`
(gap to the target, step = gap ÷ 8 rounded toward zero, the target
itself once the step is 0). Columns, face columns and "higher" are as in
`steep-faces.md`: the floor has one height per 1-px-wide column of `x`,
the object's column is the one containing its `x`, and higher means
higher on screen (smaller `y`). Q is the frame Action is let go, as in
`carrying.md`: the object leaves the player's hands on Q + 21.

Measured on the first level with the lead character alone: 135 throws by
inputs alone (standing, after walking, after turning, and with Action
let go on every frame from 3 to 40 after a jump press, both ways), 6 978
frames of thrown objects in all, ending in 62 breaks against walls and
73 removals; and about 800 controlled single frames in which the
object's height, position or speeds were set just before it reached
flat floor or a face, to find the limits below. The floor there was
flat, slopes of 1 px per 4 columns rising and falling, and faces of 4 px
per column. Hitting walkers was measured separately (see that section).
The floor below a platform that is passable from below, the platform's
edge (a face) and its top were measured separately ("Ground passable
from below").

## Each frame

From Q + 22 on, every frame, in this order:

1. `vx` follows the speed rule toward a target of **3.5** (896/256) in
   the direction the object was thrown. This goes on through bounces
   and rolling; nothing else changes `vx`.
2. **Breaking**: if the object would break against a wall (below), it
   breaks on this frame and nothing else happens.
3. `x` moves by the new `vx`.
4. The object moves vertically: in the air as in "In the air" below,
   rolling as in "Rolling".
5. **Leaving the picture**: if it is now far enough outside the picture
   (below), it is removed.

Walkers move on the same frame by their own rules. With both at their
positions at the end of the frame, the object can **hit a walker**
("Hitting walkers"); that changes nothing in the object's own motion.

## In the air

While it hasn't started rolling:

1. The new `vy` is `vy` + **0.4375** (112/256), but never above **8**
   (2048/256), as in `carrying.md`.
2. Take the new `y` = `y` + new `vy`, and the floor `F` (a level y) of
   the column of the object's new `x` ("The floor of a column" below).
   If the new `vy` is 0 or less (moving up, or not moving) and `F` is
   higher than the object's `y` before the frame, the object is
   **lifted**: it moves to the new `y` − (⌈`y`⌉ − `F`), with `y` and
   ⌈`y`⌉ from before the frame, keeps the new `vy`, and the frame ends.
3. It **touches the floor** if the new `vy` is above 0 (moving down) and
   the new `y` is less than **5 px** above the floor: `y` > `F` − 5.
   Otherwise it simply moves to the new `y` with the new `vy`. A new
   `vy` of exactly 0 never touches.
4. When it touches, its `y` becomes `F` exactly (a whole number), and
   what happens depends on its `vy` from **before** this frame (call it
   `v`, without this frame's 0.4375):
   - `v` of **1** (256/256) or more, that is moving down at least 1 px
     per frame: it **bounces**. Its `vy` becomes −⌊`v` ÷ 2⌋, in 1/256
     px (moving up at half the speed, the half rounded down: 257/256
     gives −128/256, 259/256 gives −129/256).
   - Otherwise, including when `v` was 0 or upward: it **starts
     rolling**.

### The floor of a column

The floor of a column, here and in "Rolling" and "Breaking against a
wall", is the ground below the object's point **before the frame's
move**: the ground below level x column `c` with feet at the object's
`y` (`../formats/level-ground.md`, "The ground below a point"), with
`c` the column named. That can be a surface above the object, within
the band of the grid that its feet line is in. If it is more than
**8 px** higher than the object (`F` < `y` − 8, with `y` before the
frame), the column has no floor on that frame for moving: the object
moves as in the air with nothing to touch. This limit is only for
moving (touching, being lifted, rolling). It doesn't apply to breaking:
"Breaking against a wall" uses the floor of the column however much
higher than the object it is, and a floor more than 8 px higher can
itself break the object there ("A floor more than 8 px higher").

So an object below the bottom of a platform's band (`platforms-from-below.md`)
has the floor under the platform as its floor and moves under the
platform; an object in the platform's band up to 8 px below its top is
lifted onto it or touches it; and one lower in the band moves on as if
there were no floor, until it is below the band.

So a falling object bounces every time it lands while it still had at
least 1 px per frame of speed, each bounce half as fast as the fall
before it, and the first landing slower than that makes it roll. A
throw from standing on flat floor bounces twice (see "Example").

The 5-px margin is measured: on flat floor it touched when the new `y`
was 4.98 px above the floor and didn't at 5 px exactly.

## Rolling

Once rolling, the object stays on the floor: each frame its `y` is the
floor of the column of its new `x` ("The floor of a column"), a whole
number. It doesn't bounce
or leave the floor again on the floors measured (flat, and slopes of
1 px per 4 columns going up and going down). It keeps rolling at 3.5 px
per frame until it breaks or is removed; it never stops on its own.

## Breaking against a wall

On a frame whose move would take `x` into a **different column** that

- is a face column of a face rising in the direction the object is
  moving, and
- has its floor ("The floor of a column") higher than the object's `y`
  at the start of the frame (before this frame's move, up or down), by
  any amount: the 8 px limit of "The floor of a column" doesn't apply
  here,

the object **breaks**. It doesn't move on that frame and is gone: it is
never seen in that column. Pieces fly from where it was (how they move
is not covered). The player isn't held up: walking or standing goes on
as usual on that frame and after it.

This holds rolling and in the air. In the air, an object whose `y` is
at or above the face column's floor isn't stopped: it moves into the
column and can land on it as in "In the air" (measured: with the face
column's floor 1 px higher than the floor before it, breaking up to
0.5 px below the face column's floor, landing from level with it; with
a face column 13 px higher, breaking up to 0.5 px below, landing from
level).

A move that ends in such a column breaks the object whatever columns it
passes on the way: on a face, the columns before it are lower than the
one it ends in.

**A floor more than 8 px higher.** A move into a different column whose
floor ("The floor of a column") is more than **8 px** higher than the
object's `y` at the start of the frame (`F` < `y` − 8) also breaks the
object, even if that column isn't a face column, when

- that floor is ground **not passable from below**
  (`../formats/level-ground.md`), or
- the move **passes a face column** of a face rising in the direction
  the object moves whose floor is higher than the object's `y`. The
  columns passed are those from the one next to the object's column up
  to the one the move would end in.

It breaks as against a face: it doesn't move on that frame and is gone.
This holds rolling and in the air. So a move fast enough to carry `x`
past every column of a face (whose last column is higher than the
object) breaks the object if the column it would end in is more than
8 px higher than the object, and not otherwise. Into ground passable from below with no such face column
on the way, the object isn't stopped: it moves into the column as in
"In the air", where the 8 px limit of "The floor of a column" gives it
nothing to touch.

This rule doesn't apply to an object that **came up into ground
passable from below** from the band under it ("Ground passable from
below") while it is still inside that ground, that is, while the floor
of the column it starts the frame in is higher than its `y`. Such an
object moves on inside the ground without breaking, even past a face
column of it.

Even a face column only 1 px higher than the object breaks it, while a
slope of 1 px per 4 columns (no face columns) doesn't. Rolling into a
face, the object breaks on the frame whose move would take it into the
face's first column, not before.

**Measured** (breaking with no 8 px limit) at a solid face on the first
level that rises 4 px per column, with the object moving toward it from
the flat floor before it. Controlled frames: 64 places 0.125 px apart,
7 speeds from 1 to 14.4375 px per frame (256/256 to 3696/256), heights
every 0.5 px from 2 px below the floor to 52 px above it, and `vy`
rising, 0 and falling: 146 496 starts, 432 126 frames.

- 145 490 breaks. 131 975 were moves into a face column; in 70 510 of
  those the face column was more than 8 px higher than the object (up
  to 47 px), 8 881 of them while rolling. The other 13 515 were moves
  past face columns into a column that isn't one ("A floor more than 8
  px higher"): 1 325 into ground not passable from below and 12 190
  into ground passable from below beyond the face's top.
- With the rules of this file, every frame is as measured.
- With the 8 px limit also applied to breaking, 84 025 frames would
  differ from the game.

**Measured** (a floor more than 8 px higher), besides the frames above:

- At the platform edge of "Ground passable from below" (all its ground
  passable from below, the edge a face of 4 steps of 4 px), 30 070
  frames: 200 moves from in front of the edge past its face columns
  into its top, more than 8 px higher, all broke. 817 frames of objects
  that had come up into the platform from below and moved on inside it
  with its top more than 8 px higher (26 of them past a face column of
  the edge) didn't break. In the copy with nothing passable, the same
  200 broke and every frame is as the rules give.
- The 135 throws by inputs alone of the first measurements: every one
  of the 62 breaks as the rules give, 2 of them moves past face columns
  into a column that isn't one. 83 frames moved into ground passable
  from below more than 8 px higher, with no face column on the way,
  without breaking. The 19 throws with such frames, run again in the
  copy with nothing passable, were the same up to that frame, and on it
  each one broke.
- The object put 2 to 14 px inside a solid flat floor and thrown
  toward a face at 8 to 14.4375 px per frame (2048/256 to 3696/256):
  7 131 frames, as the rules give, with 513 breaks into a column more
  than 8 px higher that isn't a face column. Put inside the platform at
  its edge in the copy with nothing passable: 9 888 frames, as the
  rules give (1 932 such breaks). In the real image, see "Not covered".
- The rule with the 8 px floor alone (no condition on passable ground or
  face columns) would differ from the game on 976 of these frames; the
  rule before this one (face columns only) on 18 313.

Whether the face is passable from below (`../formats/level-ground.md`)
makes no difference: a platform's edge that rises as a face breaks the
object as other faces do (see "Ground passable from below").

A broken object doesn't come back: in the runs measured, the view was
moved until the object's starting place was well out of the picture and
back, and nothing appeared there again.

## Ground passable from below

Whether ground is passable from below (`../formats/level-ground.md`,
"Ground passable from below") matters for a thrown object only on a
frame on which it **rises into that ground from below**:

- **Passable from below**: nothing happens; the object moves up into the
  ground's band as in "In the air", and from there the rules above
  apply (lifted onto the top, touching it, or moving on as if there were
  no floor, by "The floor of a column").
- **Not passable from below**: the object is **stopped** as a rising
  player is (`platforms-from-below.md`, "Ground that isn't passable from
  below", with the object's `y` in place of the player's feet). On a
  frame on which the new `vy` is below 0 and ⌈`y`⌉ before the frame is
  below the bottom line of a cell's band, if ⌈new `y`⌉ would be in that
  cell's ground in the column of the object's new `x`, its `y` becomes
  the new `y` + (⌈`y`⌉ − ⌈new `y`⌉), with `y` from before the frame: it
  keeps its whole px from before the frame and takes the fraction of a px
  from the move. Its `vy` becomes 0, and `x` moves as usual. On the next
  frame it falls as in "In the air".

On every other frame (falling, not moving up or down, rolling, and
breaking against a face) passable and solid ground give the same
result, except for the break of "A floor more than 8 px higher" in
"Breaking against a wall". On the first level every surface with open space under it is
passable from below, so a thrown object there is never stopped from
below.

**Measured** at the left edge of a platform on the first level whose
ground is all passable from below, including its edge, which rises as a
face of 4 steps of 4 px. Controlled frames: the object was put at 6
places from 12 px to 0.5 px before the edge's first column, at heights
from 10 px below the bottom line of the platform's band to 10 px above
its top, every 0.25 px, with `vx` 3.5, 8 and 14.4375 px per frame (896/256, 2048/256,
3696/256) moving right and `vy` −2, 0 and 2 (−512/256, 0, 512/256):
7 614 starts, 6 frames each. The same starts were run in a copy of the
image with every surface made not passable from below, and nothing else
changed.

- All 5 076 starts with `vy` 0 or 2 (not rising) gave the same frames
  in both images, including 1 216 that broke against the edge.
- Of the 2 538 rising starts, 211 differed. In each, the first
  different frame was a stop as above in the copy: in the real image
  the object rose into the platform's band.
- Frame by frame, the rules of this file gave every one of the 30 070
  frames that followed a start, in both images (with the stops in the
  copy, 133 of them). That includes 435 lifted frames, 432 breaks at
  the passable face and 200 breaks of moves past its face columns into
  its top ("A floor more than 8 px higher").
- The 135 throws by inputs alone of the first measurements (above),
  with "The floor of a column" and the lift: every frame as the rules
  give, except the frames of the 73 removals ("Leaving the picture").
  The 158 frames on which a rising object over a slope had been 1 or 2
  px higher than the rule without the lift are lifted frames.
- 86 throws by inputs alone toward the platform's edge (walking toward
  it for 12 to 40 frames and letting go, or jumping and letting go 0 to
  27 frames after the jump press): the 128 frames that started at most
  58 px past the edge's first column, as the rules give, except one
  object let go inside the platform's band 15 px below its top at 16 px
  per frame, which broke on the next frame with no face in its way
  (under "Not covered").

## Leaving the picture

Let `V` be the view's position rounded down to a whole pixel, **as it
was at the start of the frame** (as in `walker-view.md`), and `X` the
object's new `x` rounded down. The object is removed on the first frame
on which

    X − V ≤ −128   or   X − V ≥ 385

that is, once it is at least 128 px left of the picture's left edge or
at least 129 px right of the first pixel past its right edge. These are
the limits `walker-view.md` gives for walkers' own positions, but
unlike a walker, where the object was placed in the level doesn't
matter: it is removed even while its starting place is in the walker
window. A removed object didn't come back while its starting place
stayed near the picture (the only case measured).

Measured on the first level with an object picked up near its place:
2 400 throws by inputs alone (walking either way or standing before the
throw, then standing, walking, running, jumping or rolling either way,
so that the view moved at many speeds both ways), ending in 839 breaks
and 1 561 removals. 384 removals were on the left, at `X` − `V` from
−136 to −128 (53 at −128), and 1 177 on the right, from 385 to 393 (352
at 385). On the 149 772 frames the moving object stayed, `X` − `V` was
never below −127 or above 384 (87 frames at −127, 383 at 383, 388 at
384). The starting place was in the walker window on all 384 removals
on the left and 940 of those on the right. With `V` taken at the end of
the frame instead, no pair of limits fits these runs.

## Hitting walkers

The object defeats walkers (`walker.md`, sizes as in
`walker-contact.md`) that it touches, and flies or rolls on as if they
weren't there. An object the player carries defeats large walkers too,
and breaks ("A carried object" below).

Measured on the first level with the lead character alone, after
moving one ordinary object in the level's placement list so that it
rests near a walker (nothing else changed): one copy with the object
on the opening flat ahead of the first small walker, one with it inside
the patrol of the two large walkers (on flat floor and 1-in-4 slopes).
440 throws by inputs alone (standing, after walking, turning, running
and jumping, at every few frames as the walker came nearer, toward
walkers heading both ways, and from both sides), 417 hits, and 10 856
frames on which a walking walker and the thrown object were both
there, checked against the rules below with one mismatch (two walkers
in range on the same frame, "Not covered"); 10 897 frames of hit walkers checked against "The
walker", 0 mismatches; and about 17 000 controlled single frames in
which the object's position or speeds were set next to a walker just
before a frame, to find the limits.

### When a walker is hit

Let **t** be the number of frames since the object left the hands: t =
1 on Q + 22. Let **I** be the object's `x` minus the walker's `x`, both
rounded down to whole pixels, and **U** how many pixels the object's `y`
is above the walker's (the walker's `y` minus the object's `y`), both at
the end of the frame. The walker is hit on a frame when it is walking
(not already hit) and

- I is within the range in the table below for that t, and
- U is less than the height limit below for that t.

| Walker | Walker facing | Object moving | t from 1 to 16 | t of 17 or more |
|---|---|---|---|---|
| small | left | right | −48 to 28 | −23 to 27 |
| small | left | left | −29 to 47 | −28 to 22 |
| small | right | left | −28 to 48 | −27 to 23 |
| small | right | right | not measured | not measured |
| large | left | right | −51 to 31 | −26 to 30 |
| large | right | right | −50 to 32 | −25 to 31 |
| large | either | left | not measured | not measured |

Both ends are included. The height limits are:

| Walker | t from 1 to 16 | t of 17 or more |
|---|---|---|
| small | U less than 63 | U less than 30 |
| large | U less than 89 | U less than 56 |

So for its first 16 frames out of the hands (Q + 22 to Q + 37) the
object hits over a much larger area, reaching 25 px further back toward
where it came from, 1 px further ahead and 33 px higher; from Q + 38 on it hits over the
smaller area whether it is in the air, rising or falling, bouncing or
rolling. The ranges don't depend on the object's speed or on whether it
is rising or falling (measured with speeds from 3.5 to 14 px per frame).

Patterns in the measured rows: a large walker's ranges are a small
walker's widened by 3 px on each side (as for its other ranges in
`walker-contact.md`); a walker facing right has its ranges 1 px further
right than facing left; and for an object moving left the ranges are
those for an object moving right turned around (−27 to 23 from −23 to
27, and so on) and then moved 1 px left.

The walker's facing is the one of `walker.md`: while it turns at the
end of a patrol, its facing changes on T + 4, and hits on the frames
in between followed the facing it had (17 hits and about 830 frames
without a hit, all as the rules say).

The limits were measured with the walker walking at full speed. Hits
were seen with the object up to 12 px lower than the walker (U down to
−12, on slopes); lower than that wasn't seen.

One object can hit several walkers, each on its own frame (seen with
both large walkers in 67 throws).

### What else happens on a hit

- The object goes on exactly as it would have without the walker:
  same speeds, bounces, rolling and breaking.
- The player isn't held up: walking, jumping, rolling and throwing go
  on exactly as they would have without the walker (compared frame by
  frame with the walker kept away), and no frame is paused.

### The walker

Call **K** the frame on which a walker is hit and **d** the direction
the object is moving on K (+1 right, −1 left). The walker is knocked the
other way, back toward where the object came from:

1. On **K** it walks as usual (`walker.md`: speed rule, move, its `y` on
   the floor). Then its `vx` becomes **−4 × d** (4 px per frame), and
   from now on it heads the way it is knocked (−d).
2. On **K + 1**, as on a walking frame, the turn at the end of a patrol
   (`walker.md`) can still happen: if it is now heading left with `x`
   rounded down less than L, or right with `x` rounded down greater
   than R, its heading changes to the other way. Then `vx` takes one
   step of the speed rule toward **2 px per frame** in its heading
   (−1024/256 to −960/256 when heading left at −4), it moves by `vx`,
   its `y` becomes the floor's at its new `x`, and its `vy` becomes
   **−6**. (A walker's heading turned here was seen on 3 hits of a large
   walker just past the end of its patrol: from −4, `vx` stepped toward
   +2, to −832/256.)
3. From **K + 2**, every frame: `vx` takes one more step of the speed
   rule toward 2 px per frame in the same heading, `vy` increases by
   **0.4375** (7/16) but never above **8**, and it moves by both. It
   doesn't turn again and doesn't land: it falls through the floor.

There are no frozen frames (unlike a bounce, `walker.md`). On frame
K + 1 + n (n ≥ 1, before `vy` reaches 8), its `y` is
−6 × n + 0.4375 × n(n + 1) / 2 relative to its `y` on K + 1, as for a
bounced walker one frame earlier.

It is removed when it leaves the window, from K + 2 on, as in
`walker-view.md` ("Defeated walkers falling out"), and it never comes
back (`walker-view.md`, "Coming back"). Small walkers on flat floor
were there for the last time on K + 40, 95 px below the floor they had
been walking on; large walkers on slopes and flat floor between K + 38
and K + 42, 79 to 112 px below their floor on K. A walker that was
already outside the window on K + 2 was there for the last time on
K + 1.

### Example

Computed from the rules above: the throw of "Example" below (thrown
right from standing, `x` and `y` from the object's position on Q + 21,
flat floor at `y` = 54), with a small walker heading left at full speed
(facing left) on the same floor, at `x` = 160.25 on Q + 21. Speeds in
1/256 px per frame.

| Frame | Object `x` | Object `y` | Walker `x` | Walker `y` | Walker `vx` | Walker `vy` | I | U | |
|---|---|---|---|---|---|---|---|---|---|
| Q + 30 | 92.71875 | 28.6875 | 151.25 | 54 | −256 | 0 | −59 | 25.3125 | |
| Q + 31 | 99.515625 | 34.0625 | 150.25 | 54 | −256 | 0 | −51 | 19.9375 | t = 10: −51 is outside −48 to 28 |
| Q + 32 | 105.90234375 | 39.875 | 149.25 | 54 | −1024 | 0 | −44 | 14.125 | K: hit |
| Q + 33 | 111.9296875 | 46.125 | 145.5 | 54 | −960 | −1536 | | | K + 1 |
| Q + 34 | 117.64453125 | 54 | 141.96875 | 48.4375 | −904 | −1424 | | | the object bounces as before |
| Q + 35 | 123.0859375 | 51.3125 | 138.62890625 | 43.3125 | −855 | −1312 | | | |

Had the walker started 40 px further right (`x` = 200.25), the larger
area would have ended before they met: on Q + 37 (t = 16) I is −51,
outside −48 to 28; on Q + 38 (t = 17) it is −45, outside −23 to 27; the
walker is hit on Q + 43 with I = −18 and U = 8.4375, the object in the
air after its first bounce.

### A carried object

An object that holds nothing also defeats a **large** walker that it
meets while the player carries it (`carrying.md`, "Carrying"), and
breaks. A small walker on the same floor as the player is never
reached: the object is held too high, and the walker hurts the player
as usual (`walker-contact.md`; for the object, `knock-out.md`, "An
object in the hands").

Measured on the first level with the lead character alone, with the
object moved into the large walkers' patrol as above:

- by inputs: 540 runs of 420 frames. In each the player stood, walked,
  ran or jumped (three jump heights, and jumps from a run) toward a
  large walker coming back from the end of its patrol, facing it or
  turned away from it, with one or both large walkers there. Each run
  was compared with the same inputs with the walkers taken out. There
  were 406 hits. On 202 724 frames a walker walking at full speed and
  the carried object were both there; the rule below gives a hit on
  exactly the 390 of them that had one. The other 16 hits came while
  the walker was turning and weren't checked;
- controlled: 75 844 single frames. Before each frame the walker was
  put at every whole-pixel offset up to 90 px across from the object.
  The player stood facing either way, or was on one of the frames of
  carried jumps of several heights, on flat floor and on a 1-in-4
  slope. The walker was heading either way. The rule below matches all
  60 858 frames with a large walker at full speed and no bounce. A
  small walker was never hit (1 810 frames, with the object 41 to 93 px
  above it).

#### When the walker is hit

Let **I** be the object's `x` minus the walker's `x`, both rounded
down. Let **H** be the walker's `y` rounded up minus the object's `y`
rounded up (positive when the object is higher). Both are taken at the
end of the frame. A large walker that is walking (not already hit) is
hit on a frame if the player was carrying the object at the end of
the frame before, I is within the range below, and H is at most **55**:

| Walker facing | Player facing | I |
|---|---|---|
| right | right | −25 to 31 |
| right | left | −30 to 26 |
| left | right | −26 to 30 |
| left | left | −31 to 25 |

Both ends are included. For a player facing right, these are the
ranges of an object thrown right at t of 17 or more (the table under
"When a walker is hit"). For a player facing left, they are the ranges
for a player facing right moved 5 px left, as the thrower's left rows
are its right rows moved 5 px left.
Standing, walking, running and being in the air don't change them.
The lowest H measured was 11, with the player standing on the walker's
floor. Hits with H from 11 to 55 were seen; lower values weren't
measured.

On a frame on which the player bounces off the walker (`bounce.md`),
the walker isn't hit, even when the ranges hold. This was seen on 6
frames by inputs and 1 002 controlled frames.

#### What happens on a hit

Call the frame of the hit **K**.

- **The object breaks** on K: it is gone, and its pieces appear as when
  it breaks against a wall.
- **The player** isn't carrying any more. Its position and speeds on K
  and after are those it would have had with the object still carried.
  In 256 of the 406 runs they stayed the same to the end of the run. In
  the others they first differed when the player did something that
  goes differently without an object: starting a jump after K (the jump
  of a player who carries nothing), picking up another object, or
  being hurt by the other walker. On the ground it stands, walks or
  runs without the object from K + 1. In the air it keeps the airborne
  carrying pose until it lands. No frame is paused.
- **The walker** moves as under "The walker" above with d = +1,
  whichever way the player faces: its `vx` becomes −4 on K, it heads
  left from then on, and the turn at the end of its patrol can still
  happen on K + 1. This held on all 13 773 frames after the hits, with
  40 more frames excluded: those came during the pause after a
  knock-out by the other walker, when nothing moved.
- **The player can still be hurt on K + 1** by the walker that was
  hit. A hurt is decided from the positions at the end of the frame
  before (`walker-contact.md`), and at the end of K the walker still
  counts as a walking walker. This was seen once by inputs (running at
  the walker) and on the controlled frames whenever the walker had
  been put within the hurt range.

#### Example

Computed from the rules above. A large walker heading left at full
speed on flat floor at `y` = 0 (its patrol's left end further left than
`x` = 122). The player stands on the same floor facing right, carrying
the object at `x` = 100, `y` = −40, so H = 40. Speeds in px per frame.

| Frame | Walker `x` | Walker `y` | Walker `vx` | Walker `vy` | I | |
|---|---|---|---|---|---|---|
| K − 1 | 127.5 | 0 | −1 | 0 | −27 | outside −26 to 30 |
| K | 126.5 | 0 | −4 | 0 | −26 | hit; the object breaks |
| K + 1 | 122.75 | 0 | −3.75 | −6 | | |
| K + 2 | 119.21875 | −5.5625 | −3.53125 | −5.5625 | | |
| K + 3 | 115.87890625 | −10.6875 | −3.33984375 | −5.125 | | |

## Hitting the thrower

An object that holds nothing knocks the thrower (`thrower.md`) out when
it meets it: thrown, during the throw while it is still in the hands,
and while the player carries it. A thrown object goes on as if the
thrower weren't there; an object still in the hands breaks.

Measured on the first level with the lead character alone, carrying the
ordinary object that rests below the thrower's ledge: 225 runs by
inputs, each played twice, once as it is and once with the thrower taken
out of the level just before the inputs began (the control). In
these the player was put on the floor left or right of the ledge or on
the ledge itself, and threw standing, after walking, and at every few
frames of a jump, with and without a direction held. The thrower's balls
were moved out of the picture as soon as they were thrown. 197 runs had
a hit (172 of a thrown object, from 1 to 8 frames after it left the
hands, 95 moving right and 77 left; 22 during a throw; 3 while
carrying), all on the frame the rules below give; 22 had none, and the
rules gave none. Five runs were left out because the object met the
thrower before the inputs began and one because a ball broke it
("Not covered"). Separately, 559 399 controlled single frames in which
the thrown object's position and speeds, or the thrower's position, were
set just before a frame, to find the limits.

### When the thrower is hit

Let **I** be the object's `x` minus the thrower's `x`, both rounded
down, and **H** the thrower's `y` rounded up minus the object's `y`
rounded up (positive when the object is higher), both at the end of the
frame. With Q the frame Action is let go and t as under "When a walker
is hit" (t = 1 on Q + 22), the thrower is hit on a frame when it hasn't
been knocked out yet and I and H are both within these ranges (ends
included):

| The object | Thrown or facing | I | H |
|---|---|---|---|
| thrown, t from 1 to 16, or in the hands from Q + 1 to Q + 20 | right | −55 to 37 | −47 to 65 |
| the same | left | −36 to 56 | −47 to 65 |
| thrown, t of 17 or more, or carried | right | −30 to 36 | −23 to 32 |
| the same | left | −35 to 31 | −23 to 32 |

The way an object was thrown decides its row; for a carried one, the
way the player faces. Nothing is hit on Q and on Q + 21. The ranges
don't depend on the object's speed (measured from 3.5 to 14 px per
frame across, with no vertical speed and falling at 4 px per frame),
on t within each group, or, while carried,
on whether the player stands or jumps. They are the ranges of an
object that holds the missing character (`holder-breaking.md`, "The
thrower and its balls"), and the left rows are the right rows turned
around and then moved 1 px right.

### What happens on a hit

Call the frame of the hit **K**.

- **A thrown object** goes on exactly as it would have without the
  thrower: same position and speeds on every frame, same bounces,
  rolling and breaking.
- **An object in the hands** (from Q + 1 to Q + 20) **breaks** on K: it
  is gone, and its pieces appear as when it breaks against a wall. The
  throw goes on as if the object were still there: the player's
  position, speeds and pose are the same as without the thrower.
- **A carried object breaks** on K in the same way. The player is then
  no longer carrying: letting go of Action throws nothing. In the three
  hits seen, in jumps, the player's position and speeds stayed those
  of the control; it kept the airborne carrying pose until it landed
  and then stood.
- Nothing else is held up: no frame is paused, for the player or
  anything else.

The thrower, from K:

1. On **K** it doesn't move. Its `vx` becomes −4 × d (4 px per frame),
   where d is +1 if the object is moving right at the end of K and −1
   if it is moving left; an object in the hands or carried gives −4
   whichever way it faces. It doesn't move by this `vx`.
2. On **K + 1** its `vx` becomes 0 and its `vy` −5 (−1280/256), and it
   doesn't move.
3. From **K + 2**, every frame, its `vy` increases by 7/16 (112/256) but
   never above 8, and it moves by it, falling through its ledge and
   every floor below it. Its `x` never changes.

So it starts falling one frame sooner than after a bounce, a roll or
the ground action (`thrower-contact.md`, "The knocked-out thrower",
and `ground-action-enemies.md`, "The thrower"), where it doesn't move
on K + 2 either. It throws no more balls from K on (97 throws were seen
before K in the runs with a hit, none after). It is removed by the rule
of `thrower-contact.md`, "The knocked-out thrower": in all 197 runs,
on the frame that rule gives, between K + 39 and K + 47, always below
the picture.

### Example

Synthetic, from the rules above, with the thrown object of the example
at the end of this file (thrown right, `x` and `y` from its position on
Q + 21) and a thrower at `x` 115, `y` 20 that hasn't been knocked out.

| Frame | t | object `x` | object `y` | I | H | |
|---|---|---|---|---|---|---|
| Q + 22 | 1 | 14.4375 | 1.4375 | −101 | 18 | |
| Q + 23 | 2 | 27.5078125 | 3.3125 | −88 | 16 | |
| Q + 24 | 3 | 39.3828125 | 5.625 | −76 | 14 | |
| Q + 25 | 4 | 50.2109375 | 8.375 | −65 | 11 | |
| Q + 26 | 5 | 60.125 | 11.5625 | −55 | 8 | in range: K |

| Frame | thrower `y` | thrower `vx` | thrower `vy` |
|---|---|---|---|
| K | 20 | −4 | — |
| K + 1 | 20 | 0 | −5 |
| K + 2 | 15.4375 | 0 | −4.5625 |
| K + 3 | 11.3125 | 0 | −4.125 |
| K + 4 | 7.625 | 0 | −3.6875 |

The object goes on to Q + 27 at `x` 69.23828125 as without the thrower.

## Hitting a ball

A thrown object, or one still in the player's hands, removes a ball
(`thrower.md`) that it meets. A thrown object goes on as if the ball
hadn't been there; one in the hands breaks.

Measured on the first level with the lead character alone, carrying the
ordinary object that rests below the thrower's ledge, with the
thrower's balls:

- by inputs: 320 runs of 200 frames (63 680 frames). In each, the
  player stood on the deck below the ledge, or 20 or 40 px left or
  20 px right of its place there, and held the object for 0 to 150
  frames. It then threw standing, after walking, in a jump, or after
  turning to throw left. Each run was compared with the same inputs
  twice: once with every ball moved out of the picture after every
  frame, and once with the object taken out. There were 107 hits (47
  by a thrown object, 60 by one in the hands), all on the frame the
  rules below give. No ball was removed, and nothing else changed, on
  any other frame;
- controlled: 1 714 067 single frames. Before each frame a ball was
  put next to the object, at every whole-pixel offset up to 80 px
  across and 75 to 85 px up or down (60 px each way for the 76 frames
  in a row below). Each frame was compared with the same frame without
  the ball, and with it but without the object. The frames tested were:
  frames of throws to the right and to the left (on Q, on Q + 21, in
  the hands, and thrown at t = 1 to 30); the object carried while
  standing, facing left, and in a jump; and 76 frames in a row of
  carrying, so that each part of the ball's turn came up three times.
  Balls were falling straight down, rising, and moving sideways. The
  rules below match every placement.

### When a ball is hit

Let **F** be the frame. Take the object's position at the **end of F**
and the ball's position at the **end of F − 1** (before the ball moves
on F). Let **I** be the object's `x` minus the ball's `x`, both rounded
down. Let **H** be the ball's `y` rounded up minus the object's `y`
rounded up (positive when the object is higher). With t and Q as under
"When a walker is hit", the ball is hit on F when I and H are both
within these ranges (ends included):

| The object | Thrown or facing | I | H |
|---|---|---|---|
| thrown, t from 1 to 16, or in the hands from Q + 1 to Q + 20 | right | −47 to 27 | −54 to 48 |
| the same | left | −28 to 46 | −54 to 48 |
| thrown, t of 17 or more, or carried | right | −22 to 26 | −30 to 15 |
| the same | left | −27 to 21 | −30 to 15 |

These are the ranges while the ball is in the first part of its turn
(`ball-contact.md`, "The ball's turn"), as it was at the end of F − 1.
They move with the turn:

- **upside down** (second and third parts): both ends of the H range
  are 3 less (−57 to 45 early, −33 to 12 late);
- **mirrored** (third and fourth parts): both ends of the I range are
  1 more (−46 to 28 and −27 to 47 early, −21 to 27 and −26 to 22 late,
  right and left).

The way an object was thrown decides its row; for one in the hands or
carried, the way the player faces. The left rows are the right rows
turned around and then moved 1 px left. Nothing is hit on Q or on
Q + 21. Within each row and part the ranges were the same on every
frame measured, at all the speeds of these throws.

### What happens on a hit

Call the frame of the hit **K**.

- **The ball** is gone at the end of K, and something else appears
  (what it is, where, and how it is drawn are not covered).
- **A thrown object** goes on exactly as it would have without the
  ball: same position and speeds on every frame, same bounces, rolling
  and breaking. It can remove more balls later.
- **An object in the hands** (from Q + 1 to Q + 20) **breaks** on K: it
  is gone, and pieces appear. The throw goes on as if the object were
  still there: the player's position, speeds and pose are the same as
  without the ball.
- **A carried object breaks** on K in the same way, and the player is
  no longer carrying. Standing, the player has the standing pose from
  K + 1. In a jump, the player keeps the airborne carrying pose until
  it lands, then stands. Letting go of Action throws nothing. The
  player's position and speeds stay those of the run without the ball
  (one run standing, one jumping, followed for 70 frames).
- Nothing else is held up: no frame is paused, for the player or
  anything else.

### Example

Synthetic, from the rules above. The thrown object is the one in the
example at the end of this file (thrown right, `x` and `y` from its
position on Q + 21). A ball falls straight down at `x` 74, its `vy`
growing by 0.4375 each frame (`thrower.md`, "Each frame"): 3.5 on
Q + 22 and 3.9375 on Q + 23. The ball's columns are its position and
part at the end of the frame before.

| Frame | t | object `x` | object `y` | ball `x` | ball `y` | part | I | H | |
|---|---|---|---|---|---|---|---|---|---|
| Q + 22 | 1 | 14.4375 | 1.4375 | 74 | −30 | fourth | −60 | −32 | |
| Q + 23 | 2 | 27.5078125 | 3.3125 | 74 | −26.5 | fourth | −47 | −30 | out: mirrored, I from −46 |
| Q + 24 | 3 | 39.3828125 | 5.625 | 74 | −22.5625 | first | −35 | −28 | in range: K |

The ball is gone at the end of Q + 24. The object goes on to Q + 25 at
`x` 50.2109375 as it would without the ball.

## Other objects

A thrown object rolls past an object holding the missing character
without either one being affected (seen in every run that rolled past
one).

A thrown object moving right opens a closed wall exit that it comes
near (`wall-exits.md`, "Opening"), without being affected itself.

## Example

All values are computed from the rules above. Thrown to the right while
standing on flat floor; `x` and `y` from the object's position on
Q + 21, so the floor is at `y` = 54. A face rising to the right starts
200 px from there: its first column (`x` 200) has its floor at 53, 1 px
higher than the flat floor, and each column after it is 4 px higher
again. Speeds in 1/256 px per frame.

| Frame | `x` | `y` | `vx` | `vy` | |
|---|---|---|---|---|---|
| Q + 21 | 0 | 0 | 4096 | 256 | leaves the hands |
| Q + 22 | 14.4375 | 1.4375 | 3696 | 368 | |
| Q + 30 | 92.71875 | 28.6875 | 1860 | 1264 | |
| Q + 33 | 111.9296875 | 46.125 | 1543 | 1600 | |
| Q + 34 | 117.64453125 | 54 | 1463 | −800 | new `y` 52.8125 is less than 5 px above: bounces (1600 → −800) |
| Q + 35 | 123.0859375 | 51.3125 | 1393 | −688 | |
| Q + 41 | 151.60546875 | 44.375 | 1121 | −16 | top of the bounce |
| Q + 42 | 155.875 | 44.75 | 1093 | 96 | |
| Q + 45 | 168.1640625 | 48.5 | 1029 | 432 | |
| Q + 46 | 172.12109375 | 54 | 1013 | −216 | bounces (432 → −216) |
| Q + 47 | 176.0234375 | 53.59375 | 999 | −104 | |
| Q + 48 | 179.87890625 | 54 | 987 | — | touches with `v` = −104: starts rolling |
| Q + 49 | 183.69140625 | 54 | 976 | — | |
| Q + 53 | 198.61328125 | 54 | 945 | — | |
| Q + 54 | — | — | — | — | the move would take `x` to 202.28, a face column whose floor (45) is higher than `y` 54 (9 px higher: the 8 px limit doesn't apply to breaking): breaks |

## Not covered

- Hitting a character or other enemies. Walkers: a small walker facing
  right hit by an object moving right, and large walkers hit by an
  object moving left (the patterns under "When a walker is hit" would
  give −47 to 29 and −22 to 28 for the first, but this wasn't
  measured); the object more than 12 px below the walker; walkers that
  are still speeding up, outside the picture, or not on the floor; and
  two walkers in range on the same frame (seen once: only one of them
  was hit, and the other one, out of range on the next frame, wasn't).
- How a hit is drawn (something appears where the walker was hit) and
  how the hit walker is drawn while it falls.
- The thrower ("Hitting the thrower"): throwers facing right; an object
  inside the ground of the thrower's ledge, which can turn it round
  there (with H from −21 to −1 the thrower was then knocked the other
  way, by the object's new direction); whether the knocked-out thrower
  can hurt the player or give a bounce after a hit by an object (only
  its fall and removal were measured); a carried object meeting it while
  the player stands or walks by inputs (only controlled frames), and
  what the player does after a carried object breaks other than in a
  jump; a ball thrown while an object is near the thrower (once a ball
  due on that frame broke an object in the hands, the thrower wasn't
  hit, and no ball was seen); and the second character.
- Balls ("Hitting a ball"): a ball placed in the level (only the
  thrower's balls were measured); a ball on its appearance frame (the
  ball that broke an object in the hands in the item above did so on
  the frame it was due, which the rule, with the ball's position at the
  end of the frame before, doesn't give); two balls, or a ball and the
  thrower, within reach on the same frame; frames on which the ball
  bounces or stops ignoring floors, as cases of their own (some were
  among the frames measured by inputs); a carried object broken in runs
  by inputs (only controlled frames, two of them followed on); an
  object that holds the missing character (`holder-breaking.md` gives
  its ranges from the ball's position at the end of the frame, measured
  with one ball falling straight down; whether the turn and the earlier
  position apply to it too wasn't checked); and the second character.
- A carried object and walkers ("A carried object"): small walkers on
  a floor high enough above the player's to come within reach (their
  limits for a carried object weren't measured), medium walkers,
  walkers that are turning or speeding up, H below 11, a `holder`
  carried into a walker, two walkers in range on the same frame, what
  happens to the carried object when the player bounces off a walker,
  and the second character. The ranges are given for the object's
  position; where a carried object is relative to the player is not
  covered (`carrying.md`).
- Places that stop walkers (`walker.md`, "Turning at a barrier"): near
  the one measured, a thrown object moving left was seen to break with
  no wall there, and a carried object was knocked out of the player's
  hands.
- An object inside ground passable from below that it didn't come up
  into from below. One let go inside a platform's band broke on the
  next frame with no face column in its way (seen once, moving 16 px
  per frame), and so did objects put inside the platform by setting
  their position, moving into a column more than 8 px higher (504
  controlled frames). Objects that flew into such ground from the side
  and moved on into columns no higher than their own didn't (59 frames).
- Faces of other steepness.
- Rolling off a ledge or down a vertical step, and slopes other than
  1 px per 4 columns.
- How the pieces of a broken object move and are drawn, and how the
  object is drawn while bouncing and rolling.
- Objects thrown by the second character.
- Removal above or below the picture (only horizontal positions were
  measured).
