# Thrown objects: bouncing, rolling and breaking

Titles: `title-1`

What a thrown object that holds nothing does from the moment it is
thrown until it is gone: how it bounces on the floor, how it then rolls,
how it breaks against a wall, what happens when it hits a walker, and
when it is removed after leaving the picture. `carrying.md` covers the throw itself and the flight up to the
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
   the column of the object's new `x`.
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

So a falling object bounces every time it lands while it still had at
least 1 px per frame of speed, each bounce half as fast as the fall
before it, and the first landing slower than that makes it roll. A
throw from standing on flat floor bounces twice (see "Example").

The 5-px margin is measured: on flat floor it touched when the new `y`
was 4.98 px above the floor and didn't at 5 px exactly.

## Rolling

Once rolling, the object stays on the floor: each frame its `y` is the
floor of the column of its new `x`, a whole number. It doesn't bounce
or leave the floor again on the floors measured (flat, and slopes of
1 px per 4 columns going up and going down). It keeps rolling at 3.5 px
per frame until it breaks or is removed; it never stops on its own.

## Breaking against a wall

On a frame whose move would take `x` into a **different column** that

- is a face column of a face rising in the direction the object is
  moving, of ground that is not passable from below
  (`../formats/level-ground.md`), and
- has its floor higher than the object's `y` at the start of the frame
  (before this frame's move, up or down),

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

Even a face column only 1 px higher than the object breaks it, while a
slope of 1 px per 4 columns (no face columns) doesn't. Rolling into a
face, the object breaks on the frame whose move would take it into the
face's first column, not before.

A broken object doesn't come back: in the runs measured, the view was
moved until the object's starting place was well out of the picture and
back, and nothing appeared there again.

## Leaving the picture

Let `V` be the view's position rounded down to a whole pixel (as in
`walker-view.md`) and `X` the object's new `x` rounded down. The object
was removed on frames where `X` − `V` was **385** or **386**, and never
while `X` − `V` was 382 or less; 383 and 384 weren't seen. This is the
limit `walker-view.md` gives for walkers (`X` − `V` ≥ 385), so at least
129 px right of the first pixel past the picture's right edge. Being
removed on the left wasn't seen. A removed object didn't come back
while its starting place stayed near the picture (the only case
measured).

## Hitting walkers

The object defeats walkers (`walker.md`, sizes as in
`walker-contact.md`) that it touches, and flies or rolls on as if they
weren't there.

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

It is then removed. Small walkers on flat floor last moved on K + 40,
95 px below the floor they had been walking on; large walkers on slopes
and flat floor last moved between K + 38 and K + 42, 79 to 112 px below
their floor on K. A walker hit while it was well left of the picture
(its `x` rounded down minus `V`, as in "Leaving the picture", −38 or
less on K + 1) last moved on K + 1; from −35 on it fell as above. When
exactly it is removed isn't specified.

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
| Q + 54 | — | — | — | — | the move would take `x` to 202.28, a face column whose floor (45) is higher than `y` 54: breaks |

## Not covered

- Hitting a character or other enemies. Walkers: a small walker facing
  right hit by an object moving right, and large walkers hit by an
  object moving left (the patterns under "When a walker is hit" would
  give −47 to 29 and −22 to 28 for the first, but this wasn't
  measured); the object more than 12 px below the walker; walkers that
  are still speeding up, outside the picture, or not on the floor; and
  two walkers in range on the same frame (seen once: only one of them
  was hit, and the other one, out of range on the next frame, wasn't).
- When exactly a hit walker is removed, and whether it comes back.
- How a hit is drawn (something appears where the walker was hit) and
  how the hit walker is drawn while it falls.
- A walker walking into an object that the player is carrying (seen to
  defeat the walker; the object was gone).
- Places that stop walkers (`walker.md`, "Turning at a barrier"): near
  the one measured, a thrown object moving left was seen to break with
  no wall there, and a carried object was knocked out of the player's
  hands.
- Being in the air over a slope: rising over floor that rises under it,
  the object was sometimes lifted 1 or 2 px higher than the rule above
  gives (all 158 such frames were above slopes; none on flat floor).
- A move that carries `x` past face columns into a column that isn't
  one (seen three times, the object broke each time), ground other than
  faces that is higher than the object, such as the edge of a platform
  that is passable from below (seen to break it in some cases and not in
  others), and faces of other steepness.
- Landing on floor up to 8 px higher than the object, seen in controlled
  frames at the edge of a platform; whether this holds generally.
- Rolling off a ledge or down a vertical step, and slopes other than
  1 px per 4 columns.
- How the pieces of a broken object move and are drawn, and how the
  object is drawn while bouncing and rolling.
- Removal on the left of the picture, and `X` − `V` of 383 and 384.
- Objects thrown by the second character.
